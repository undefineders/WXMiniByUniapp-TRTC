<template>
<view>
<!--index.wxml-->
<view class="trtc-demo-container">
  <view class="title" :style="'padding-top:' + ((headerHeight + statusBarHeight)/2 - 12) + 'px'">
    <view>双人通话</view>
  </view>
  <view class="input-box">
    <input type="number" :value="roomID" maxlength="10" @input="enterRoomID" placeholder="请输入房间号" placeholder-style="color:#ffffff;opacity: 0.55;"></input>
  </view>
  <view class="choice-content">
    <label class="label" for="switchDebugMode">
      <text>调试模式</text>
      <switch id="switchDebugMode" color="#006eff" :checked="debugMode" @change="switchDebugMode"></switch>
    </label>
  </view>
</view>

<view class="bottom-btn">
  <button type="primary" @tap="enterRoom" hover-class="none">进入房间</button>
</view>
<cover-image class="close" :style="'top:' + ((headerHeight + statusBarHeight) - 34) + 'rpx'" src="../../static/images/back.png" @tap="onBack"></cover-image>
</view>
</template>

<script>
import { setData } from "../../debug/GenerateTestUserSig";
// index.js
// const app = getApp().globalData;
const app = getApp().globalData;

export default {
  data() {
    return {
      roomID: '',
      template: '1v1',
      debugMode: false,
      cloudenv: 'PRO',
      evnArray: [{
        value: 'PRO',
        title: 'PRO'
      }, {
        value: 'CCC',
        title: 'CCC'
      }, {
        value: 'DEV',
        title: 'DEV'
      }, {
        value: 'UAT',
        title: 'UAT'
      }],
	  tapTime:'',
      headerHeight: getApp().globalData.headerHeight,
      statusBarHeight: getApp().globalData.statusBarHeight
    };
  },

  components: {},
  props: {},
  onLoad: function () {},
  methods: {
	  setData,
    enterRoomID: function (event) {
      console.log('index enterRoomID', event);
      this.setData({
        roomID: event.detail.value
      });
    },
    selectTemplate: function (event) {
      console.log('index selectTemplate', event);
      this.setData({
        template: event.detail.value
      });
    },
    switchDebugMode: function (event) {
      console.log('index switchDebugMode', event);
      this.setData({
        debugMode: event.detail.value
      });
    },
    selectEnv: function (event) {
      console.log('index switchDebugMode', event);
      this.setData({
        cloudenv: event.detail.value
      });
    },
    enterRoom: function () {
      const roomID = this.roomID;
      const nowTime = new Date();

      if (nowTime - this.tapTime < 1000) {
        return;
      }

      if (!roomID) {
        wx.showToast({
          title: '请输入房间号',
          icon: 'none',
          duration: 2000
        });
        return;
      }

      if (/^\d*$/.test(roomID) === false) {
        wx.showToast({
          title: '房间号只能为数字',
          icon: 'none',
          duration: 2000
        });
        return;
      }

      if (roomID > 4294967295 || roomID < 0) {
        wx.showToast({
          title: '房间号取值范围为 1~4294967295',
          icon: 'none',
          duration: 2000
        });
        return;
      }

      const url = `../room/room?roomID=${roomID}&template=${this.template}&debugMode=${this.debugMode}&cloudenv=${this.cloudenv}`;
      wx.navigateTo({
        url: url
      });
      this.setData({
        'tapTime': nowTime
      });
    },
    onBack: function () {
      wx.navigateBack({
        delta: 1
      });
    }
  }
};
</script>
<style>
@import "./videocall.css";
</style>