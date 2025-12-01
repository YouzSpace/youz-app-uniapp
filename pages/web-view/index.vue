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
      this.webUrl = options.url;
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