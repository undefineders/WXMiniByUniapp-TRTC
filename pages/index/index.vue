<template>
<view>
<!--index.wxml-->
<view class="container">
  <view class="title" :style="'padding-top:' + ((headerHeight + statusBarHeight)/2 - 12) + 'px'">
    <text>腾讯实时音视频</text>
  </view>
   <view class="tips">
    <text>以下将展示小程序实时音视频能力，由腾讯云提供技术支持</text>
  </view>
  <view class="guide-box">
    <view v-for="(item, index) in entryInfos" :key="index" class="single-box" :id="index" @tap="handleEntry">
      <image class="icon" mode="aspectFit" :src="item.icon" role="img"></image>
      <view class="label">{{item.title}}</view>
      <view class="desc">{{item.desc}}</view>
    </view>
  </view>
</view>

<view class="logo-box">
  <image class="logo" src="/static/images/logo.png"></image>
</view>
</view>
</template>

<script>
// index.js
// const app = getApp()
const app = getApp();

export default {
  data() {
    return {
      template: '1v1',
      headerHeight: app.globalData.headerHeight,
      statusBarHeight: app.globalData.statusBarHeight,
      entryInfos: [{
        icon: "/static/images/call.png",
        title: "语音聊天室",
        desc: "<trtc-room>",
        navigateTo: "../voice-room/join-room/joinRoom"
      }, {
        icon: "/static/images/doubleroom.png",
        title: "双人通话",
        desc: "<trtc-room>",
        navigateTo: "../videocall/videocall"
      }, {
        icon: "/static/images/multiroom.png",
        title: "多人会议",
        desc: "<trtc-room>",
        navigateTo: "../meeting/meeting"
      }]
    };
  },

  components: {},
  props: {},
  onLoad: function () {},
  methods: {
    selectTemplate: function (event) {
      console.log('index selectTemplate', event);
      this.setData({
        template: event.detail.value
      });
    },
    handleEntry: function (e) {
      let url = this.entryInfos[e.currentTarget.id].navigateTo;
      uni.navigateTo({
        url: url
      });
    }
  }
};
</script>
<style>
/**index.wxss**/

.container {
  background-image: url("https://mc.qcloudimg.com/static/img/7da57e0050d308e2e1b1e31afbc42929/bg.png");
  background-color: #333;
  background-repeat:no-repeat;
  background-size: cover;
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  box-sizing: border-box;
}

.container .title{
  color: #FFFFFF;
  padding-top: 65rpx;
  padding-bottom: 40rpx;
  line-height: 60rpx;
}
.tips {
  color: #ffffff;
  font-size: 12px;
  text-align: center;
}
.guide-box {
  display: flex;
  flex-wrap: wrap;
  text-align: center;
  padding: 100rpx 5vw;
}
.single-box {
  width: 35vw;
  height: 33vw;
  background-color: rgba(0, 0, 0, 0.55);
  text-align: center;
  vertical-align: top;
  margin: 5vw ;
  margin-bottom: 60rpx;
}
.icon {
  display: block;
  width: 72rpx;
  height: 72rpx;
  margin: 20px auto 0;
}
.label {
  display: block;
  margin-top: 10px;
  color: #CFE4FF;
  font-size: 28rpx;
}
.desc {
  display: block;
  margin-top: 2px;
  color: #CFE4FF;
  font-size: 28rpx;
  white-space: nowrap;
}
.logo-box {
  position: absolute;
  width: 100vw;
  bottom: 36rpx;
  text-align: center;
}
.logo {
  width: 160rpx;
  height: 44rpx;
}

</style>