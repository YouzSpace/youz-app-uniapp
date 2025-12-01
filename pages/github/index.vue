<template>
  <view class="github-container">
    <view class="header">
      <text class="title">GitHub</text>
    </view>
    <view class="content">
      <view class="github-info">
        <image class="logo" src="/static/icons/github.png" mode="aspectFit"></image>
        <text class="github-desc">欢迎访问柚子云Store的GitHub页面</text>
        <text class="github-note">这里是我们的开源项目集合</text>
        <text class="version-info">当前版本：{{ currentVersion }}</text>
        <button class="check-update-btn" @click="checkUpdate">检查更新</button>
        
        <view class="project-list">
          <view class="project-item" @click="openProject('main-repo')">
            <text class="project-name">youz-app-uniapp</text>
            <text class="project-desc">柚子云Store前端应用</text>
            <text class="visit-btn">访问项目</text>
          </view>
          
          <view class="project-item" @click="openProject('api-repo')">
            <text class="project-name">youz-app-app</text>
            <text class="project-desc">柚子云Store后端API</text>
            <text class="visit-btn">访问项目</text>
          </view>
        </view>
        
        <text class="star-info">如果您觉得项目有用，请给我们点个Star⭐</text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      title: 'GitHub',
      currentVersion: '1.0.0', // 初始版本号
      appUpdateInfo: null
    }
  },
  onLoad() {
      // 页面加载时，从manifest.json获取版本号
      this.getCurrentVersion();
    },
    methods: {
      // 获取当前版本号（从manifest.json）
      getCurrentVersion() {
        try {
          // 在uni-app中，plus.runtime.version可以获取应用的版本名称
          if (typeof plus !== 'undefined' && plus.runtime) {
            this.currentVersion = plus.runtime.version;
          } else {
            // H5环境下，可以尝试从manifest.json中读取
            // 或者保留默认值
            console.log('plus对象不可用，使用默认版本号');
          }
        } catch (error) {
          console.error('获取版本号失败', error);
        }
      },
      // 检查更新
      checkUpdate() {
        uni.showLoading({
          title: '检查更新中...'
        });
        
        // 获取云端版本信息
        this.fetchCloudVersion();
      },
    
    // 获取云端版本信息
    fetchCloudVersion() {
      const apiUrl = 'https://store.youz.space//view.php?id=181419';
      
      uni.request({
        url: apiUrl,
        method: 'GET',
        success: (res) => {
          uni.hideLoading();
          
          if (res.statusCode === 200 && res.data && res.data.appUpdate) {
            this.appUpdateInfo = res.data.appUpdate;
            // 比较版本号
            if (this.compareVersions(res.data.appUpdate.version, this.currentVersion) > 0) {
              // 有新版本，显示更新提示
              this.showUpdateDialog();
            } else {
              uni.showToast({
                title: '已是最新版本',
                icon: 'none',
                duration: 2000
              });
            }
          } else {
            uni.showToast({
              title: '获取更新信息失败',
              icon: 'none',
              duration: 2000
            });
          }
        },
        fail: (err) => {
          uni.hideLoading();
          console.error('获取云端版本失败', err);
          uni.showToast({
            title: '网络异常，检查更新失败',
            icon: 'none',
            duration: 2000
          });
        }
      });
    },
    
    // 比较版本号
    compareVersions(version1, version2) {
      const v1 = version1.split('.').map(Number);
      const v2 = version2.split('.').map(Number);
      
      for (let i = 0; i < Math.max(v1.length, v2.length); i++) {
        const num1 = v1[i] || 0;
        const num2 = v2[i] || 0;
        
        if (num1 > num2) return 1;
        if (num1 < num2) return -1;
      }
      
      return 0;
    },
    
    // 显示更新对话框
    showUpdateDialog() {
      if (!this.appUpdateInfo) return;
      
      // 使用uni-app的弹窗组件
      uni.showModal({
        title: '发现新版本',
        content: this.generateUpdateContent(),
        showCancel: true,
        confirmText: '去更新',
        cancelText: '暂不更新',
        success: (res) => {
          if (res.confirm) {
            // 用户点击去更新，跳转到下载页面
            this.openDownloadPage();
          }
        }
      });
    },
    
    // 生成更新内容文本
    generateUpdateContent() {
      if (!this.appUpdateInfo) return '';
      
      let content = `新版本: ${this.appUpdateInfo.version}\n\n更新内容:`;
      
      if (this.appUpdateInfo.updateContent && Array.isArray(this.appUpdateInfo.updateContent)) {
        this.appUpdateInfo.updateContent.forEach((item, index) => {
          content += `\n${index + 1}. ${item}`;
        });
      }
      
      return content;
    },
    
    // 打开下载页面
    openDownloadPage() {
      if (!this.appUpdateInfo || !this.appUpdateInfo.downloadUrl) {
        uni.showToast({
          title: '下载地址无效',
          icon: 'none',
          duration: 2000
        });
        return;
      }
      
      const downloadUrl = this.appUpdateInfo.downloadUrl.trim();
      
      // 封装uni.openURL调用
      const tryOpenWithUni = (url) => {
        uni.openURL({
          url: url,
          success: () => {
            console.log('打开下载页面成功');
          },
          fail: (err) => {
            console.error('打开下载页面失败', err);
            uni.showToast({
              title: '无法打开下载页面，请复制链接到浏览器访问',
              icon: 'none',
              duration: 2000
            });
            // 复制链接到剪贴板，方便用户手动打开
            uni.setClipboardData({
              data: url,
              success: () => {
                uni.showToast({
                  title: '下载链接已复制到剪贴板',
                  icon: 'success',
                  duration: 1500
                });
              }
            });
          }
        });
      };
      
      // 首先尝试使用plus.runtime.openURL
      if (typeof plus !== 'undefined' && plus.runtime && plus.runtime.openURL) {
        plus.runtime.openURL(downloadUrl, function(res) {
          console.log('使用plus.runtime.openURL打开下载页面成功');
        }, function(error) {
          console.error('使用plus.runtime.openURL打开下载页面失败', error);
          // 如果plus.runtime.openURL失败，尝试使用uni.openURL作为备选
          tryOpenWithUni(downloadUrl);
        });
      } else {
        // 如果plus对象不可用，使用uni.openURL
        tryOpenWithUni(downloadUrl);
      }
    },
    openProject(projectType) {
      // 这里可以根据不同项目类型打开不同的GitHub链接
      let url = 'https://github.com/YouzSpace';
      if (projectType === 'main-repo') {
        url = 'https://github.com/YouzSpace/youz-app-uniapp';
      } else if (projectType === 'api-repo') {
        url = 'https://github.com/YouzSpace/apps';
      }
      
      // 封装uni.openURL调用
      const tryOpenWithUni = (url) => {
        uni.openURL({
          url: url,
          success: () => {
            console.log('打开GitHub项目成功');
          },
          fail: (err) => {
            console.error('打开GitHub项目失败', err);
            uni.showToast({
              title: '无法打开链接，请复制链接到浏览器访问',
              icon: 'none',
              duration: 2000
            });
            // 复制链接到剪贴板，方便用户手动打开
            uni.setClipboardData({
              data: url,
              success: () => {
                uni.showToast({
                  title: '链接已复制到剪贴板',
                  icon: 'success',
                  duration: 1500
                });
              }
            });
          }
        });
      };
      
      // 首先尝试使用plus.runtime.openURL（更可靠的跨平台方案）
      if (typeof plus !== 'undefined' && plus.runtime && plus.runtime.openURL) {
        plus.runtime.openURL(url, function(res) {
          console.log('使用plus.runtime.openURL打开成功');
        }, function(error) {
          console.error('使用plus.runtime.openURL打开失败', error);
          // 如果plus.runtime.openURL失败，尝试使用uni.openURL作为备选
          tryOpenWithUni(url);
        });
      } else {
        // 如果plus对象不可用，使用uni.openURL
        tryOpenWithUni(url);
      }
    }
  }
}
</script>

<style scoped>
.github-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  background-color: #F3F4F6;
}

.header {
  padding: 20rpx;
  padding-top: calc(20rpx + var(--status-bar-height, 44rpx));
  background-color: #4F46E5;
  text-align: center;
}

.title {
  font-size: 36rpx;
  color: white;
  font-weight: bold;
}

.content {
  flex: 1;
  padding: 40rpx 20rpx;
  display: flex;
  justify-content: center;
}

.github-info {
  width: 100%;
  background-color: white;
  border-radius: 12rpx;
  padding: 40rpx;
  text-align: center;
}

.logo {
  width: 160rpx;
  height: 160rpx;
  margin-bottom: 30rpx;
}

.github-desc {
  display: block;
  font-size: 32rpx;
  color: #333;
  font-weight: bold;
  margin-bottom: 10rpx;
}

.github-note {
  display: block;
  font-size: 28rpx;
  color: #666;
  margin-bottom: 40rpx;
}

.project-list {
  width: 100%;
}

.project-item {
  width: 100%;
  background-color: #F9FAFB;
  border-radius: 8rpx;
  padding: 20rpx;
  margin-bottom: 20rpx;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  transition: background-color 0.3s;
}

.project-item:active {
  background-color: #F3F4F6;
}

.project-name {
  font-size: 28rpx;
  font-weight: bold;
  color: #333;
  margin-bottom: 5rpx;
}

.project-desc {
  font-size: 24rpx;
  color: #666;
  margin-bottom: 10rpx;
}

.visit-btn {
  font-size: 24rpx;
  color: #4F46E5;
  padding: 5rpx 15rpx;
  border: 1px solid #4F46E5;
  border-radius: 4rpx;
  margin-top: 10rpx;
}

.version-info {
  display: block;
  font-size: 28rpx;
  color: #666;
  margin-top: 20rpx;
  margin-bottom: 30rpx;
}

.check-update-btn {
  width: 240rpx;
  height: 70rpx;
  line-height: 70rpx;
  font-size: 28rpx;
  color: #4F46E5;
  background-color: white;
  border: 1px solid #4F46E5;
  border-radius: 35rpx;
  margin-bottom: 40rpx;
  padding: 0;
}

.check-update-btn:active {
  background-color: #F3F4F6;
}

.star-info {
  display: block;
  font-size: 26rpx;
  color: #F59E0B;
  margin-top: 40rpx;
}
</style>