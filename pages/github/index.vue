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
  </view>pp
</template>

<script>
export default {
  data() {
    return {
      title: 'GitHub'
    }
  },
  onLoad() {
    // 页面加载时的逻辑
  },
  methods: {
    openProject(projectType) {
      // 这里可以根据不同项目类型打开不同的GitHub链接
      let url = 'https://github.com/YouzSpace';
      if (projectType === 'main-repo') {
        url = 'https://github.com/YouzSpace/youz-app-uniapp';
      } else if (projectType === 'api-repo') {
        url = 'https://github.com/YouzSpace/apps';
      }
      
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
      
      // 封装uni.openURL调用
      function tryOpenWithUni(url) {
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

.star-info {
  display: block;
  font-size: 26rpx;
  color: #F59E0B;
  margin-top: 40rpx;
}
</style>