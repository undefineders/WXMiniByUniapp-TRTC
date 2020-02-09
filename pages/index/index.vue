<template>
<view>
<!--index.wxml-->
<view class="container">
  <view class="title" :style="'padding-top:' + ((headerHeight + statusBarHeight)/2 - 12) + 'px'">
    <text>腾讯实时音视频</text>
  </view>
  <view class="wrapper">
    <radio-group class="choice-content" @change="selectTemplate">
      <view class="single-wrapper">
        <label class="label radio-label">
          <radio color="#006eff" value="1v1" :checked="template==='1v1'?'true':''" ref="1v1"></radio>
          <text>双人通话</text>
        </label>
        <label for="1v1" :class="'preview-item ' + (template==='1v1'?'selected':'')">
          <image src="../../static/images/1v1.png"></image>
        </label>
      </view>
      <view class="single-wrapper">
        <label class="label radio-label">
          <radio color="#006eff" value="grid" :checked="template==='grid'?'true':''" ref="grid"></radio>
          <text>多人会议</text>
        </label>
        <label for="grid" :class="'preview-item ' + (template==='grid'?'selected':'')">
          <image src="../../static/images/grid.png"></image>
        </label>
      </view>
    </radio-group>
  </view>
</view>

<view class="bottom-btn">
  <button type="primary" @tap="enterRoom" hover-class="none">进入模板</button>
</view>
</view>
</template>

<script>
// index.js
// const app = getApp().globalData;
const app = getApp().globalData;

export default {
  data() {
    return {
      template: '1v1',
      headerHeight: getApp().globalData.headerHeight,
      statusBarHeight: getApp().globalData.statusBarHeight
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
    enterRoom: function () {
      let url = '../videocall/videocall';

      if (this.template === 'grid') {
        url = '../meeting/meeting';
      }

      wx.navigateTo({
        url: url
      });
    }
  }
};
</script>
<style>
@import "./index.css";
</style>