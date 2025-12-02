<template>
  <view class="web-view-container">
    <view class="header">
      <text class="back-button" @click="goBack">←</text>
      <text class="title">{{ title }}</text>
    </view>
    <web-view :src="webUrl"></web-view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      title: '文档',
      webUrl: ''
    }
  },
  onLoad(options) {
    // 从页面参数中获取URL和标题
    if (options.url) {
      // 对于本地HTML文件，需要使用正确的路径格式
      if (options.url.startsWith('/static/')) {
        // 在uni-app中，web-view加载本地HTML文件需要使用相对路径
        // 移除开头的斜杠，使用相对路径格式
        this.webUrl = options.url.substring(1);
      } else {
        this.webUrl = options.url;
      }
    }
    if (options.title) {
      this.title = options.title;
    }
  },
  methods: {
    goBack() {
      uni.navigateBack();
    }
  }
}
</script>

<style scoped>
.web-view-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  background-color: #F3F4F6;
}

.header {
  display: flex;
  align-items: center;
  padding: 20rpx;
  padding-top: calc(20rpx + var(--status-bar-height, 44rpx));
  background-color: #4F46E5;
  position: relative;
  z-index: 10;
}

.back-button {
  font-size: 48rpx;
  color: white;
  margin-right: 20rpx;
}

.title {
  font-size: 36rpx;
  color: white;
  font-weight: bold;
  flex: 1;
  text-align: center;
  margin-right: 60rpx;
}

web-view {
  flex: 1;
  width: 100%;
}
</style>