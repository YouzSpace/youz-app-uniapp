<template>
	<view class="container">
		<!-- 搜索栏和分类栏容器 -->
		<view class="search-category-container">
			<!-- 搜索栏 -->
			<view class="search-section">
				<view class="search-box">
					<input 
						v-model="searchQuery" 
						placeholder="搜索软件、游戏、应用..."
						@input="onSearchInput"
						class="search-input"
					/>
					<view class="search-icon">🔍</view>
				</view>
			</view>

			<!-- 分类标签 -->
			<view class="category-section" v-if="categories.length > 0">
				<view class="category-tabs">
					<view 
						v-for="category in categories" 
						:key="category.id"
						:class="['tab-item', { active: activeCategory === category.id }]"
						@click="switchCategory(category.id)"
					>
						{{ category.name }}
					</view>
				</view>
			</view>
		</view>

		<!-- 应用列表 -->
		<view class="apps-grid">
			<view 
			v-for="app in filteredApps" 
			:key="app.id"
			class="app-card"
			@click="downloadApp(app)"
		>
				<view class="app-content">
					<image :src="app.icon" class="app-icon" mode="aspectFill" @error="onImageError"></image>
					<view class="app-info">
						<view class="app-name">{{ app.name }}</view>
						<view class="app-description">{{ app.description }}</view>
					</view>
				</view>
				<view class="app-footer">
					<view class="download-btn" @click.stop="downloadApp(app)">下载</view>
				</view>
			</view>
		</view>

		<!-- 加载更多 -->
		<view v-if="hasMore" class="load-more-container" @click="loadMore">
			<view class="load-more" :class="{ loading: isLoading }">
				<text>{{ isLoading ? '加载中...' : '点击加载更多' }}</text>
			</view>
		</view>

		<!-- 空状态 -->
		<view v-if="filteredApps.length === 0 && !isLoading" class="empty-state">
			<view class="empty-icon">📱</view>
			<view class="empty-text">暂无相关应用</view>
			<view class="empty-desc">请尝试其他搜索关键词</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			searchQuery: '',
			activeCategory: 'all',
			isLoading: false,
			hasMore: true,
			currentPage: 1,
			appsPerPage: 20,
			apps: [],
			allCategories: [
				{ id: 'all', name: '全部' },
				{ id: 'AE工程', name: 'AE工程' },
				{ id: 'XP模块', name: 'XP模块' },
				{ id: '谷歌应用', name: '谷歌应用' }
			],
			categories: [],
			searchTimer: null
		}
	},
	computed: {
		filteredApps() {
			let result = this.apps;
			
			// 按分类筛选
			if (this.activeCategory !== 'all') {
				result = result.filter(app => app.category === this.activeCategory);
			}
			
			// 按搜索关键词筛选
			if (this.searchQuery.trim()) {
				const query = this.searchQuery.toLowerCase();
				result = result.filter(app => 
					app.name.toLowerCase().includes(query) ||
					app.description.toLowerCase().includes(query) ||
					app.category.toLowerCase().includes(query)
				);
			}
			
			return result;
		}
	},
	async onLoad() {
		await this.loadAppsFromAPI();
		this.extractCategories();
	},
	onPullDownRefresh() {
		this.refreshData();
	},
	onReachBottom() {
		this.loadMore();
	},
	methods: {
		onSearchInput(e) {
			clearTimeout(this.searchTimer);
			this.searchTimer = setTimeout(() => {
				this.loadAppsFromAPI(true);
			}, 500);
		},
		
		switchCategory(categoryId) {
			this.activeCategory = categoryId;
			this.filterAppsByCategory();
		},
		
	async loadAppsFromAPI(resetData = false) {
		if (this.isLoading) return;
		
		this.isLoading = true;
		
		if (resetData) {
			this.currentPage = 1;
		}
		
		try {
			// 使用正确的uni.request方式
			const res = await new Promise((resolve, reject) => {
				uni.request({
					url: `https://store.youz.space/api.php?user_id=youzapi&page=${this.currentPage}&limit=${this.appsPerPage}`,
					method: 'GET',
					success: (response) => {
						resolve(response);
					},
					fail: (error) => {
						reject(error);
					}
				});
			});
			
			if (!res.data || res.statusCode !== 200) {
				throw new Error('API请求失败');
			}
			
			if (res.data.error) {
				throw new Error(res.data.error);
			}
			
			const data = res.data;
			
			// 处理返回的数据
			const newApps = data.documents.map((doc, index) => {
				const appName = doc.name || '未知应用';
				
				return {
					id: parseInt(doc.id) || index + 1,
					name: appName,
					description: doc.subtitle || `${appName} - 功能强大的应用程序`,
					category: this.inferCategory(appName, doc.subtitle),
					rating: this.generateRandomRating(),
					downloads: this.generateRandomDownloads(),
					size: this.generateRandomSize(),
					version: '1.0.0',
					updateDate: this.generateRandomDate(),
					icon: doc.icon || 'https://via.placeholder.com/64x64/CCCCCC/FFFFFF?text=APP',
					downloadUrl: `https://store.youz.space/view.php?id=${doc.id}`
				};
			});
			
			// 更新数据
			if (resetData) {
				this.apps = newApps;
			} else {
				this.apps = [...this.apps, ...newApps];
			}
			
			// 更新分页信息
			if (data.pagination) {
				this.hasMore = data.pagination.has_next;
			} else {
				this.hasMore = newApps.length >= this.appsPerPage;
			}
			
			// 提取分类
			if (resetData) {
				this.extractCategories();
			}
			
		} catch (error) {
			console.error('加载失败:', error);
		} finally {
			this.isLoading = false;
		}
	},
		
		extractCategories() {
			// 只保留我们需要的三个分类
			this.categories = [
				{ id: 'all', name: '全部' },
				{ id: 'AE工程', name: 'AE工程' },
				{ id: 'XP模块', name: 'XP模块' },
				{ id: '谷歌应用', name: '谷歌应用' }
			];
		},
		
		filterAppsByCategory() {
			// 这个方法在computed中已经处理了
			this.$forceUpdate();
		},
		
		async refreshData() {
			this.currentPage = 1;
			await this.loadAppsFromAPI(true);
			uni.stopPullDownRefresh();
		},
		
		async loadMore() {
			if (this.isLoading || !this.hasMore) return;
			
			this.currentPage++;
			await this.loadAppsFromAPI(false);
		},
		
		openApp(app) {
			// 显示应用详情
			let content = `开发者：${app.developer}\n分类：${app.category}\n版本：${app.version}\n大小：${app.size}\n下载：${app.downloads}次\n\n${app.description}`;
			
			uni.showModal({
				title: app.name,
				content: content,
				showCancel: true,
				cancelText: '取消',
				confirmText: '下载',
				success: (res) => {
					if (res.confirm) {
						this.downloadApp(app);
					}
				}
			});
		},
		
		downloadApp(app) {
			uni.showActionSheet({
				itemList: ['浏览器打开', '复制链接'],
				success: (res) => {
					if (res.tapIndex === 0) {
						// 浏览器打开
						plus.runtime.openURL(app.downloadUrl);
					} else if (res.tapIndex === 1) {
						// 复制链接
						uni.setClipboardData({
							data: app.downloadUrl,
							success: () => {
								uni.showToast({
									title: '链接已复制',
									icon: 'success'
								});
							}
						});
					}
				}
			});
		},
		
		// 工具方法 - 推断分类
		inferCategory(name, subtitle) {
			const keywords = {
				'AE工程': ['ae', 'after effects', '模板', '特效', '动效', 'pr', 'premiere'],
				'XP模块': ['xp', 'xposed'],
				'谷歌应用': ['google', '谷歌']
			};
			
			const text = (name + ' ' + (subtitle || '')).toLowerCase();
			
			for (const [category, words] of Object.entries(keywords)) {
				if (words.some(word => text.includes(word))) {
					return category;
				}
			}
			
			// 不再将无关应用自动归入谷歌应用分类，改为返回一个不会显示的默认值
			return '未分类';
		},
		
		// 工具方法 - 推断开发者
		inferDeveloper(name) {
			if (name.includes('Adobe')) return 'Adobe Inc.';
			if (name.includes('Microsoft')) return 'Microsoft Corporation';
			if (name.includes('Google')) return 'Google LLC';
			if (name.includes('腾讯')) return 'Tencent';
			if (name.includes('阿里')) return 'Alibaba';
			if (name.includes('百度')) return 'Baidu';
			if (name.includes('字节')) return 'ByteDance';
			if (name.includes('华为')) return 'Huawei';
			if (name.includes('小米')) return 'Xiaomi';
			if (name.includes('OPPO')) return 'OPPO';
			if (name.includes('vivo')) return 'vivo';
			
			return '柚子软件库';
		},
		
		// 生成随机评分
		generateRandomRating() {
			return (Math.random() * 2 + 3).toFixed(1); // 3.0-5.0
		},
		
		// 生成随机下载量
		generateRandomDownloads() {
			const random = Math.floor(Math.random() * 100000) + 1000;
			if (random > 10000) {
				return Math.floor(random / 1000) + 'k+';
			}
			return random.toString();
		},
		
		// 生成随机大小
		generateRandomSize() {
			const sizes = ['12.5MB', '28.3MB', '45.7MB', '67.2MB', '89.4MB', '125.8MB', '234.1MB'];
			return sizes[Math.floor(Math.random() * sizes.length)];
		},
		
		// 生成随机日期
		generateRandomDate() {
			const now = new Date();
			const days = Math.floor(Math.random() * 30);
			const date = new Date(now - days * 24 * 60 * 60 * 1000);
			return date.toISOString().split('T')[0];
		},
		
		// 图片加载错误处理
		onImageError(e) {
			e.target.src = 'https://via.placeholder.com/64x64/CCCCCC/FFFFFF?text=ICON';
		}
	}
}
</script>

<style scoped>
.container {
	padding: 0;
	background: transparent;
	min-height: 100vh;
	overflow-y: auto;
}

/* 搜索栏和分类栏容器 */
.search-category-container {
	position: fixed;
	top: 0;
	left: 0;
	right: 0;
	z-index: 999;
}

/* 搜索栏 */
.search-section {
	background: linear-gradient(135deg, #4285f4 0%, #669df6 50%, #3367d6 100%);
	padding: 20rpx;
	padding-top: calc(var(--status-bar-height) + 20rpx);
	height: 120rpx;
}

.search-box {
	position: relative;
	background: white;
	border-radius: 50rpx;
	padding: 20rpx 60rpx 20rpx 30rpx;
	box-shadow: 0 4rpx 12rpx rgba(60, 64, 67, 0.3);
}

.search-input {
	width: 100%;
	height: 60rpx;
	font-size: 28rpx;
	color: #202124;
}

.search-icon {
	position: absolute;
	right: 30rpx;
	top: 50%;
	transform: translateY(-50%);
	font-size: 32rpx;
	color: #9aa0a6;
}

/* 分类标签区域 */
.category-section {
	background: linear-gradient(135deg, #ffffff 0%, #f8f9fa 30%, #ffffff 100%);
	border-bottom: 1rpx solid #dadce0;
	padding-top: 30rpx; /* 增加上边距，确保分类栏完全显示在搜索栏下方 */
}

/* 分类标签 - 保持原版设计 */
.category-tabs {
	padding: 20rpx;
	display: flex;
	justify-content: space-around;
	flex-wrap: wrap;
	gap: 16rpx;
}

.tab-item {
	padding: 16rpx 32rpx;
	border-radius: 24rpx;
	font-size: 26rpx;
	color: #5f6368;
	transition: all 0.3s;
	background: linear-gradient(135deg, #f8f9fa 0%, #ffffff 30%, #e8eaed 100%);
	border: 1rpx solid #dadce0;
}

.tab-item.active {
	background: linear-gradient(135deg, #4285f4 0%, #669df6 50%, #3367d6 100%);
	color: white;
	border-color: transparent;
}

/* 应用列表 - 完全匹配原版样式 */
.apps-grid {
	display: grid;
	grid-template-columns: 1fr;
	gap: 24rpx;
	padding: 20rpx;
	padding-top: 20rpx;
	margin-top: 260rpx; /* 将应用列表向上移动20px */
	background: #f8f9fa;
}

/* 隐藏滚动条 */
::-webkit-scrollbar {
	display: none !important;
	width: 0 !important;
	height: 0 !important;
	background: transparent !important;
}

/* 针对不同平台的滚动条隐藏 */
scroll-view ::-webkit-scrollbar {
	display: none !important;
	width: 0 !important;
	height: 0 !important;
}

/* UniApp滚动条隐藏 */
view ::-webkit-scrollbar {
	display: none !important;
	width: 0 !important;
	height: 0 !important;
}

/* 针对整个页面的滚动条 */
page {
	overflow: hidden;
}

page::-webkit-scrollbar {
	display: none !important;
	width: 0 !important;
	height: 0 !important;
}

.app-card {
	background: linear-gradient(135deg, #ffffff 0%, #fafbfc 30%, #f8f9fa 100%);
	border: 1rpx solid #dadce0;
	border-radius: 16rpx;
	padding: 24rpx;
	transition: all 0.3s ease;
	cursor: pointer;
	color: inherit;
	display: flex;
	justify-content: space-between;
	align-items: center;
	box-shadow: 0 2rpx 8rpx rgba(60, 64, 67, 0.3);
}

.app-card:active {
	background: linear-gradient(135deg, #f0f4ff 0%, #e6f0ff 30%, #d8e7ff 100%);
	transform: translateY(-4rpx);
	box-shadow: 0 16rpx 48rpx rgba(66, 133, 244, 0.15);
	border-color: #4285f4;
}

.app-content {
	display: flex;
	align-items: center;
	gap: 24rpx;
	flex: 1;
}

.app-icon {
	width: 96rpx;
	height: 96rpx;
	border-radius: 8rpx;
	object-fit: cover;
	flex-shrink: 0;
}

.app-info {
	display: flex;
	flex-direction: column;
	gap: 8rpx;
}

.app-name {
	font-weight: 600;
	font-size: 32rpx;
	color: #202124;
}

.app-description {
	color: #5f6368;
	font-size: 28rpx;
	line-height: 1.4;
	display: -webkit-box;
	-webkit-line-clamp: 2;
	-webkit-box-orient: vertical;
	overflow: hidden;
}

.app-footer {
	display: flex;
	justify-content: center;
	align-items: center;
}

.download-btn {
	background: linear-gradient(135deg, #4285f4 0%, #669df6 50%, #3367d6 100%);
	color: white;
	border: none;
	padding: 16rpx 24rpx;
	border-radius: 8rpx;
	font-weight: 500;
	cursor: pointer;
	transition: all 0.3s ease;
	box-shadow: 0 4rpx 16rpx rgba(44, 49, 57, 0.3);
	font-size: 28rpx;
}

.download-btn:active {
	background: linear-gradient(135deg, #3367d6 0%, #4285f4 50%, #669df6 100%);
	transform: translateY(-2rpx);
	box-shadow: 0 8rpx 24rpx rgba(57, 58, 59, 0.4);
}

/* 加载更多 - 匹配原版样式 */
.load-more-container {
	display: flex;
	justify-content: center;
	align-items: center;
	margin-top: 48rpx;
	width: 100%;
	padding: 0 20rpx;
}

.load-more {
	background: linear-gradient(135deg, #f8f9fa 0%, #ffffff 30%, #e8eaed 100%);
	border: 1rpx solid #dadce0;
	color: #5f6368;
	padding: 24rpx 48rpx;
	border-radius: 8rpx;
	cursor: pointer;
	transition: all 0.3s ease;
	font-weight: 500;
	opacity: 1;
	transform: translateY(0);
	font-size: 28rpx;
}

.load-more:active {
	background: linear-gradient(135deg, #4285f4 0%, #669df6 50%, #3367d6 100%);
	border-color: transparent;
	color: white;
	transform: translateY(-4rpx);
	box-shadow: 0 16rpx 40rpx rgba(66, 133, 244, 0.3);
}

/* 空状态 - 保持原版设计风格 */
.empty-state {
	text-align: center;
	padding: 120rpx 40rpx;
}

.empty-icon {
	font-size: 120rpx;
	margin-bottom: 20rpx;
}

.empty-text {
	font-size: 32rpx;
	color: #202124;
	margin-bottom: 12rpx;
	font-weight: 500;
}

.empty-desc {
	font-size: 26rpx;
	color: #5f6368;
}
</style>