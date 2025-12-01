<script>
export default {
	onLaunch: function() {
		console.log('App Launch')
	},
	onShow: function() {
		console.log('App Show')
	},
	onHide: function() {
		console.log('App Hide')
	}
}
</script>

<style>
/*每个页面公共css */
@import url("@/static/css/common.css");

page {
	background-color: #f3f4f6;
	font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
}

.container {
	padding: 20rpx;
}

/* uni-app 重置样式 */
view, text, navigator, image {
	box-sizing: border-box;
}

/* 按钮样式 */
.btn {
	padding: 20rpx 40rpx;
	border-radius: 12rpx;
	font-size: 28rpx;
	text-align: center;
}

.btn-primary {
	background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
	color: white;
}

.btn-secondary {
	background: #6b7280;
	color: white;
}

/* 卡片样式 */
.card {
	background: white;
	border-radius: 16rpx;
	padding: 24rpx;
	margin-bottom: 20rpx;
	box-shadow: 0 2rpx 8rpx rgba(0,0,0,0.1);
}
</style>