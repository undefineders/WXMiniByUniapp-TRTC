<template>
<view>
<view class="trtc-demo-container">
  <view class="title" :style="'padding-top:' + ((headerHeight + statusBarHeight)/2 - 12) + 'px'">
    <view>多人会议</view>
	</view>
	<view class="input-box">
    <input type="number" :value="roomID" maxlength="10" @input="enterRoomID" placeholder="请输入房间号" placeholder-style="color:#ffffff;opacity: 0.55;"></input>
	</view>
	<view class="input-box">
    <input type="text" :value="userID" maxlength="10" @input="enterUserID" placeholder="请输入用户名(英文+数字)" placeholder-style="color:#ffffff;opacity: 0.55;"></input>
    <button class="btn btn-random" hover-class="btn-hover" @tap="randomUserID">随机</button>
	</view>
	<view class="choice-content">
		<text>场景选择</text>
    <radio-group class="radio-group-choice" @change="selectScene">
      <label>
          <radio color="#006eff" value="rtc" checked="true" id="open"></radio>通话</label>
      <label>
          <radio color="#006eff" value="live" id="close"></radio>直播</label>
    </radio-group>
  </view>
  <view class="choice-content">
    <text>本地镜像</text>
    <radio-group class="radio-group-no-box" @change="selectLocalMirror">
      <label v-for="(item, index) in localMirrorArray" :key="index" :class="'radio-item ' + ( localMirror == item.value ? 'selected': '')">
        <radio :value="item.value" :checked="item.checked"></radio>{{item.title}}</label>
    </radio-group>
  </view>
  <view class="choice-content">
    <label class="label" for="switchEarMonitor">
      <text>开启耳返</text>
      <switch id="switchEarMonitor" color="#006eff" :checked="enableEarMonitor" @change="switchEarMonitor"></switch>
    </label>
    <label class="label" for="switchAgc">
      <text>自动增益</text>
      <view class="switch-right">
        <switch id="switchAgc" color="#006eff" :checked="enableAgc" @change="switchAgc"></switch>
      </view>
    </label>
  </view>
  <view class="choice-content">
    <label class="label" for="switchAutoFocus">
      <text>自动对焦</text>
      <switch id="switchAutoFocus" color="#006eff" :checked="enableAutoFocus" @change="switchAutoFocus"></switch>
    </label>
    <label class="label" for="switchAns">
      <text>噪声消除</text>
      <view class="switch-right">
        <switch id="switchAns" color="#006eff" :checked="enableAns" @change="switchAns"></switch>
      </view>
    </label>
  </view>
  <view class="choice-content">
    <label class="label" for="switchLocalVideo">
      <text>本地视频</text>
      <switch id="switchLocalVideo" color="#006eff" :checked="localVideo" @change="switchLocalVideo"></switch>
    </label>
    <label class="label" for="switchLocalAudio">
      <text>本地音频</text>
      <view class="switch-right">
        <switch id="switchLocalAudio" color="#006eff" :checked="localAudio" @change="switchLocalAudio"></switch>
      </view>
    </label>
  </view>
  <view class="choice-content">
		<label class="label" for="switchDebugMode">
      <text>调试模式</text>
      <switch id="switchDebugMode" color="#006eff" :checked="debugMode" @change="switchDebugMode"></switch>
    </label>
    <label class="label" for="switchSmallScreen">
      <text>启用小画面</text>
      <view class="switch-right">
        <switch id="switchSmallScreen" color="#006eff" :checked="encsmall" @change="switchSmallScreen"></switch>
      </view>
    </label>
  </view>
  <view class="choice-content">
      <text class="device-position">摄像头选择</text>
      <radio-group class="radio-group-choice" @change="selectDevicePosition">
				<label>
					<radio color="#006eff" value="front" checked="true" id="open"></radio>前置</label>
				<label>
					<radio color="#006eff" value="back" id="close"></radio>后置</label>
      </radio-group>
	</view>
  <view class="choice-content">
    <text>视频分辨率</text>
    <radio-group class="radio-group-no-box" @change="selectResolution">
      <label v-for="(item, index) in resolutionArray" :key="index" :class="'radio-item ' + ( resolution == item.value ? 'selected': '')">
        <radio :value="item.value" :checked="item.checked"></radio>{{item.title}}</label>
    </radio-group>
  </view>
</view>

<view class="bottom-btn">
  <button type="primary" @tap="enterRoom" hover-class="none">进入房间</button>
</view>
<cover-image class="close" :style="'top:' + ((headerHeight + statusBarHeight) - 34) + 'rpx'" src="../../images/back.png" @tap="onBack"></cover-image>
</view>
</template>

<script>
// miniprogram/pages/meeting.js
const app = getApp().globalData;

export default {
  data() {
    return {
      roomID: '',
      userID: '',
      template: 'grid',
      cloudenv: 'PRO',
      scene: 'rtc',
      localVideo: true,
      localAudio: true,
      enableEarMonitor: false,
      enableAutoFocus: true,
      localMirror: 'auto',
      enableAgc: true,
      enableAns: true,
      encsmall: false,
      frontCamera: 'front',
      resolution: 'FHD',
      debugMode: false,
      // 用于自定义输入视频分辨率
      videoHeight: 720,
      videoWidth: 1280,
      minBitrate: 1500,
      maxBitrate: 2000,
      localMirrorArray: [{
        value: 'auto',
        title: '自动'
      }, {
        value: 'enable',
        title: '开启'
      }, {
        value: 'disable',
        title: '关闭'
      }],
      resolutionArray: [{
        value: 'FHD',
        title: 'FHD'
      }, {
        value: 'HD',
        title: 'HD'
      }, {
        value: 'SD',
        title: 'SD'
      } // { value: 'default', title: '自定义' }
      ],
      headerHeight: getApp().globalData.headerHeight,
      statusBarHeight: getApp().globalData.statusBarHeight
    };
  },

  components: {},
  props: {},

  /**
   * 生命周期函数--监听页面加载
   * @param {Object} options 参数
   */
  onLoad: function (options) {
    wx.setKeepScreenOn({
      keepScreenOn: true
    }); // 随机 userID roomID
    // this.setData({
    //   roomID: parseInt(10000 * Math.random()),
    //   userID: new Date().getTime().toString(16),
    // })
  },

  /**
   * 生命周期函数--监听页面初次渲染完成
   */
  onReady: function () {},

  /**
   * 生命周期函数--监听页面显示
   */
  onShow: function () {},

  /**
   * 生命周期函数--监听页面隐藏
   */
  onHide: function () {},

  /**
   * 生命周期函数--监听页面卸载
   */
  onUnload: function () {},

  /**
   * 页面相关事件处理函数--监听用户下拉动作
   */
  onPullDownRefresh: function () {},

  /**
   * 页面上拉触底事件的处理函数
   */
  onReachBottom: function () {},

  /**
   * 用户点击右上角分享
   */
  onShareAppMessage: function () {},
  methods: {
    enterRoomID: function (e) {
      this.setData({
        roomID: e.detail.value
      });
    },
    enterUserID: function (e) {
      this.setData({
        userID: e.detail.value
      });
    },
    selectScene: function (e) {
      this.setData({
        scene: e.detail.value
      });
    },
    switchLocalVideo: function (e) {
      this.setData({
        localVideo: e.detail.value
      });
    },
    switchLocalAudio: function (e) {
      this.setData({
        localAudio: e.detail.value
      });
    },
    switchEarMonitor: function (e) {
      this.setData({
        enableEarMonitor: e.detail.value
      });
    },
    switchAutoFocus: function (e) {
      this.setData({
        enableAutoFocus: e.detail.value
      });
    },
    switchAgc: function (e) {
      this.setData({
        enableAgc: e.detail.value
      });
    },
    switchAns: function (e) {
      this.setData({
        enableAns: e.detail.value
      });
    },
    switchSmallScreen: function (e) {
      this.setData({
        encsmall: e.detail.value
      });
    },
    selectDevicePosition: function (e) {
      this.setData({
        frontCamera: e.detail.value
      });
    },
    selectLocalMirror: function (e) {
      this.setData({
        localMirror: e.detail.value
      });
    },
    switchDebugMode: function (e) {
      this.setData({
        debugMode: e.detail.value
      });
    },
    selectResolution: function (e) {
      this.setData({
        resolution: e.detail.value
      }, () => {
        // 如果用户选择自定义的话, 手动输入分辨率，并且在传递的时候进行一下判断
        switch (this.resolution) {
          case 'FHD':
            this.setData({
              videoWidth: 720,
              videoHeight: 1280,
              minBitrate: 1500,
              maxBitrate: 2000
            });
            break;

          case 'SD':
            this.setData({
              videoWidth: 360,
              videoHeight: 640,
              minBitrate: 600,
              maxBitrate: 900
            });
            break;

          case 'HD':
            this.setData({
              videoWidth: 540,
              videoHeight: 960,
              minBitrate: 1000,
              maxBitrate: 1500
            });
            break;

          default:
            console.log('choose resolution error');
            break;
        }
      });
    },
    enterRoom: function () {
      const nowTime = new Date();

      if (nowTime - this.tapTime < 1000) {
        return;
      }

      const url = `../room/room?roomID=${this.roomID}` + `&template=${this.template}` + `&debugMode=${this.debugMode}` + `&cloudenv=${this.cloudenv}` + `&localVideo=${this.localVideo}` + `&localAudio=${this.localAudio}` + `&enableEarMonitor=${this.enableEarMonitor}` + `&enableAutoFocus=${this.enableAutoFocus}` + `&localMirror=${this.localMirror}` + `&enableAgc=${this.enableAgc}` + `&enableAns=${this.enableAns}` + `&encsmall=${this.encsmall}` + `&frontCamera=${this.frontCamera}` + `&videoWidth=${this.videoWidth}` + `&videoHeight=${this.videoHeight}` + `&scene=${this.scene}` + `&userID=${this.userID}` + `&minBitrate=${this.minBitrate}` + `&maxBitrate=${this.maxBitrate}`;

      if (!this.roomID) {
        wx.showToast({
          title: '请输入房间号',
          icon: 'none',
          duration: 2000
        });
        return;
      }

      if (!this.userID) {
        wx.showToast({
          title: '请输入用户名',
          icon: 'none',
          duration: 2000
        });
        return;
      }

      const reg = /^[0-9a-zA-Z]*$/;

      if (this.userID.match(reg) === null) {
        wx.showToast({
          icon: 'none',
          title: '用户名为英文加数字'
        });
      } else {
        wx.navigateTo({
          url: url
        });
        this.setData({
          'tapTime': nowTime
        });
      }
    },
    onBack: function () {
      wx.navigateBack({
        delta: 1
      });
    },
    randomUserID: function () {
      this.setData({
        // roomID: parseInt(10000 * Math.random()),
        userID: new Date().getTime().toString(16)
      });
    }
  }
};
</script>
<style>
@import "./meeting.css";
</style>