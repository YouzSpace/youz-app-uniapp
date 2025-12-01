<template>
  <view class="disclaimer-container">
    <view class="header">
      <text class="title">免责声明</text>
    </view>
    <scroll-view class="content" scroll-y>
      <view class="announcement">
        <view class="announcement-header">
          <text class="announcement-icon">📢</text>
          <text class="announcement-title">公告</text>
        </view>
        <view class="announcement-content">
          <text class="announcement-text">{{ announcementText }}</text>
        </view>
      </view>
      <view class="disclaimer-content">
        <text class="paragraph">1. 本站提供的所有软件及相关资源，仅可供用户用于学习和交流，请用户在使用过程中严格遵循国家相关法律法规及行业规范，不得用于任何违法违规用途。</text>
        <text class="paragraph">2. 本站所分享的资源内容均为网络收集整理所得，不代表本站立场。若资源存在侵权、违规、内容不妥等情况，版权方或相关方请及时联系本站，本站在收到通知后将第一时间进行核查并删除相关内容，由此带来的不便敬请谅解。</text>
        <text class="paragraph">3. 本站发布的破解补丁、注册机、注册信息及软件解密分析文章等内容，仅限用于学习和研究目的。用户不得将上述内容用于商业活动或者非法用途，否则，一切后果由用户自负，与本站无关。</text>
        <text class="paragraph">4. 您必须在下载本站资源后的24个小时之内，从您的电脑及相关存储设备中彻底删除上述内容。若您对相关程序或软件感兴趣，建议支持正版软件，通过正规渠道购买注册，以获得更完善的正版服务与保障。</text>
        <text class="paragraph">5. 本站信息来源于网络，所有资源的版权归属原作者或相关权利人。若因资源引发版权争议，相关责任与本站无关。如涉及侵权问题，请版权方通过邮箱 youz.space@foxmail.com 与我们联系处理，我们将积极配合。</text>
        <text class="paragraph">请您在使用本站资源前仔细阅读本声明，一旦您下载或使用本站资源，即视为您已同意并接受本声明的全部内容。</text>
        
        <view class="agreement-links">
          <text class="link" @click="openServiceAgreement">查看服务协议</text>
          <text class="link" @click="openPrivacyPolicy">查看隐私政策</text>
        </view>
        
        <text class="update-time">更新时间：2025年</text>
      </view>
    </scroll-view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      title: '免责声明',
      announcementText: '欢迎使用柚子云Store！我们将定期更新优质软件资源，敬请期待。如有任何建议或问题，请联系我们。'
    }
  },
  onLoad() {
    // 页面加载时的逻辑
  },
  methods: {
    // 可以在这里添加页面相关的方法
    openServiceAgreement() {
      uni.navigateTo({
        url: '/pages/web-view/index?url=/static/service-agreement.html&title=服务协议'
      });
    },
    openPrivacyPolicy() {
      uni.navigateTo({
        url: '/pages/web-view/index?url=/static/privacy-policy.html&title=隐私政策'
      });
    }
  }
}
</script>

<style scoped>
.disclaimer-container {
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
  padding: 20rpx;
}

.announcement {
  background-color: #FFFBEB;
  border: 1rpx solid #FBBF24;
  border-radius: 12rpx;
  padding: 20rpx;
  margin-bottom: 20rpx;
}

.announcement-header {
  display: flex;
  align-items: center;
  margin-bottom: 10rpx;
}

.announcement-icon {
  font-size: 32rpx;
  margin-right: 10rpx;
}

.announcement-title {
  font-size: 30rpx;
  font-weight: bold;
  color: #92400E;
}

.announcement-content {
  padding-left: 42rpx;
}

.announcement-text {
  font-size: 26rpx;
  color: #92400E;
  line-height: 40rpx;
}

.disclaimer-content {
  background-color: white;
  border-radius: 12rpx;
  padding: 30rpx;
}

.section-title {
  display: block;
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
  margin-top: 30rpx;
  margin-bottom: 20rpx;
}

.paragraph {
  display: block;
  font-size: 28rpx;
  color: #666;
  line-height: 44rpx;
  margin-bottom: 20rpx;
}

.agreement-links {
  margin: 30rpx 0;
  display: flex;
  justify-content: center;
  gap: 40rpx;
}

.link {
  color: #4F46E5;
  font-size: 28rpx;
  text-decoration: underline;
  padding: 10rpx 0;
}

.update-time {
  display: block;
  font-size: 24rpx;
  color: #999;
  text-align: right;
  margin-top: 20rpx;
}
</style>