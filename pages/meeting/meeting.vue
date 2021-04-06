<template>
<view>
<view class="trtc-demo-container">
  <view class="title" :style="'padding-top:' + ((headerHeight + statusBarHeight)/2 - 12) + 'px'">
    <view>多人会议</view>
	</view>
	<view class="input-box">
    <input type="number" :value="roomID" maxlength="20" data-key="roomID" @input="enterHandler" placeholder="请输入房间号" placeholder-style="color:#ffffff;opacity: 0.55;"></input>
	</view>
	<view class="input-box">
    <input type="text" :value="userID" maxlength="10" data-key="userID" @input="enterHandler" placeholder="请输入用户名(英文+数字)" placeholder-style="color:#ffffff;opacity: 0.55;"></input>
    <button class="btn btn-random" hover-class="btn-hover" @tap="randomUserID">随机</button>
	</view>
  <view class="main">
    <scroll-view class="scroll-container" scroll-y="true">
      <view class="scroll-content">
        <view class="choice-content">
          <text>场景选择</text>
          <radio-group class="radio-group-no-box" data-key="scene" @change="selectHandler">
            <label v-for="(item, index) in sceneArray" :key="index" :class="'radio-item ' + ( scene == item.value ? 'selected': '')">
              <radio :value="item.value" :checked="item.checked"></radio> {{item.title}}
            </label>
          </radio-group>
        </view>
        <view class="choice-content">
          <label class="label" for="switchEarMonitor">
            <text>开启耳返</text>
            <switch id="switchEarMonitor" color="#006eff" :checked="enableEarMonitor" data-key="enableEarMonitor" @change="switchHandler"></switch>
          </label>
          <label class="label" for="switchAgc">
            <text>自动增益</text>
            <view class="switch-right">
              <switch id="switchAgc" color="#006eff" :checked="enableAgc" data-key="enableAgc" @change="switchHandler"></switch>
            </view>
          </label>
        </view>
        <view class="choice-content">
          <label class="label" for="switchAutoFocus">
            <text>自动对焦</text>
            <switch id="switchAutoFocus" color="#006eff" :checked="enableAutoFocus" data-key="enableAutoFocus" @change="switchHandler"></switch>
          </label>
          <label class="label" for="switchAns">
            <text>噪声消除</text>
            <view class="switch-right">
              <switch id="switchAns" color="#006eff" :checked="enableAns" data-key="enableAns" @change="switchHandler"></switch>
            </view>
          </label>
        </view>
        <view class="choice-content">
          <label class="label" for="switchLocalVideo">
            <text>本地视频</text>
            <switch id="switchLocalVideo" color="#006eff" :checked="localVideo" data-key="localVideo" @change="switchHandler"></switch>
          </label>
          <label class="label" for="switchLocalAudio">
            <text>本地音频</text>
            <view class="switch-right">
              <switch id="switchLocalAudio" color="#006eff" :checked="localAudio" data-key="localAudio" @change="switchHandler"></switch>
            </view>
          </label>
        </view>
        <view class="choice-content">
          <label class="label" for="switchDebugMode">
            <text>调试模式</text>
            <switch id="switchDebugMode" color="#006eff" :checked="debugMode" data-key="debugMode" @change="switchHandler"></switch>
          </label>
          <label class="label" for="switchSmallScreen">
            <text>启用小画面</text>
            <view class="switch-right">
              <switch id="switchSmallScreen" color="#006eff" :checked="encsmall" data-key="encsmall" @change="switchHandler"></switch>
            </view>
          </label>
        </view>
        <view class="choice-content">
            <text class="device-position">摄像头选择</text>
            <radio-group class="radio-group-choice" data-key="frontCamera" @change="selectHandler">
              <label>
                <radio color="#006eff" value="front" checked="true" id="open"></radio>
                前置
              </label>
              <label>
                <radio color="#006eff" value="back" id="close"></radio>
                后置
              </label>
            </radio-group>
        </view>
        <view class="choice-content">
          <text>音量类型</text>
          <radio-group class="radio-group-no-box" data-key="audioVolumeType" @change="selectHandler">
            <label v-for="(item, index) in audioVolumeTypeArray" :key="index" :class="'radio-item ' + ( audioVolumeType == item.value ? 'selected': '')">
              <radio :value="item.value" :checked="item.checked"></radio> {{item.title}}
            </label>
          </radio-group>
        </view>
        <view class="choice-content">
          <text>本地镜像</text>
          <radio-group class="radio-group-no-box" data-key="localMirror" @change="selectHandler">
            <label v-for="(item, index) in localMirrorArray" :key="index" :class="'radio-item ' + ( localMirror == item.value ? 'selected': '')">
              <radio :value="item.value" :checked="item.checked"></radio> {{item.title}}
            </label>
          </radio-group>
        </view>
        <view class="choice-content">
          <text>视频分辨率</text>
          <radio-group class="radio-group-no-box" data-key="resolution" @change="selectHandler">
            <label v-for="(item, index) in resolutionArray" :key="index" :class="'radio-item ' + ( resolution == item.value ? 'selected': '')">
              <radio :value="item.value" :checked="item.checked"></radio> {{item.title}}
            </label>
          </radio-group>
        </view>
      </view>
    </scroll-view>
  </view>
  <view class="bottom-btn">
    <button class="btn-enter" @tap="enterRoom" hover-class="none">进入房间</button>
  </view>  
</view>

<cover-image class="close" :style="'top:' + ((headerHeight + statusBarHeight) - 34) + 'rpx'" src="../../static/images/back.png" @tap="onBack"></cover-image>
</view>
</template>

<script>
// miniprogram/pages/meeting.js
const app = getApp();

export default {
  data() {
    return {
      roomID: '',
      userID: '',
      template: 'grid',
      localVideo: true,
      localAudio: true,
      enableEarMonitor: false,
      enableAutoFocus: true,
      localMirror: 'auto',
      enableAgc: true,
      enableAns: true,
      frontCamera: 'front',
      audioVolumeType: 'auto',
      resolution: 'SD',
      debugMode: false,
      audioQuality: 'high',
      // 用于自定义输入视频分辨率和默认值
      videoWidth: 360,
      videoHeight: 640,
      minBitrate: 600,
      maxBitrate: 900,
      // pusher URL 参数
      scene: 'rtc',
      encsmall: false,
      cloudenv: 'PRO',
      enableBlackStream: 0,
      streamID: '',
      userDefineRecordID: '',
      privateMapKey: '',
      pureAudioMode: '',
      // 默认不填，值为1或者2
      recvMode: '',
      // player 参数
      enableRecvMessage: false,
      audioQualityArray: [{
        value: 'high',
        title: '48K'
      }, {
        value: 'low',
        title: '16K'
      }],
      cloudenvArray: [{
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
      sceneArray: [{
        value: 'rtc',
        title: '通话'
      }, {
        value: 'live',
        title: '直播'
      }],
      audioVolumeTypeArray: [{
        value: 'auto',
        title: '自动'
      }, {
        value: 'media',
        title: '媒体'
      }, {
        value: 'voicecall',
        title: '通话'
      }],
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
      }],
      headerHeight: app.globalData.headerHeight,
      statusBarHeight: app.globalData.statusBarHeight
    };
  },

  components: {},
  props: {},

  /**
   * 生命周期函数--监听页面加载
   * @param {Object} options 参数
   */
  onLoad: function (options) {
    uni.setKeepScreenOn({
      keepScreenOn: true
    }); // this.randomUserID()
    // 随机 userID roomID
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
    enterHandler: function (event) {
      const key = event.currentTarget.dataset.key;
      const data = {};
      data[key] = event.detail.value;

      if ('roomID' === key) {
        data[key] = data[key].replace(/[^A-Za-z0-9]/g, '');
      }

      this.setData(data, () => {
        console.log(`set ${key}:`, data[key]);
      });
    },
    switchHandler: function (event) {
      const key = event.currentTarget.dataset.key;
      const data = {};
      data[key] = event.detail.value;

      if (key === 'enableBlackStream') {
        data[key] = data[key] === false ? 0 : 1;
      }

      this.setData(data, () => {
        console.log(`set ${key}:`, data[key]);
      });
    },
    selectHandler: function (event) {
      const key = event.currentTarget.dataset.key;
      const data = {};
      data[key] = event.detail.value;
      this.setData(data, () => {
        console.log(`set ${key}:`, data[key]);

        if ('resolution' === key) {
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
              break;
          }
        }
      });
    },
    enterRoom: function () {
      const nowTime = new Date();

      if (nowTime - this.tapTime < 1200) {
        return;
      }

      const url = `../room/room?roomID=${this.roomID}` + `&template=${this.template}` + `&debugMode=${this.debugMode}` + `&localVideo=${this.localVideo}` + `&localAudio=${this.localAudio}` + `&enableEarMonitor=${this.enableEarMonitor}` + `&enableAutoFocus=${this.enableAutoFocus}` + `&localMirror=${this.localMirror}` + `&enableAgc=${this.enableAgc}` + `&enableAns=${this.enableAns}` + `&frontCamera=${this.frontCamera}` + `&audioVolumeType=${this.audioVolumeType}` + `&audioQuality=${this.audioQuality}` + `&videoWidth=${this.videoWidth}` + `&videoHeight=${this.videoHeight}` + `&userID=${this.userID}` + `&minBitrate=${this.minBitrate}` + `&maxBitrate=${this.maxBitrate}` + // pusher URL 参数
      `&encsmall=${this.encsmall}` + `&scene=${this.scene}` + `&cloudenv=${this.cloudenv}` + `&enableBlackStream=${this.enableBlackStream}` + `&streamID=${this.streamID}` + `&userDefineRecordID=${this.userDefineRecordID}` + `&privateMapKey=${this.privateMapKey}` + `&pureAudioMode=${this.pureAudioMode}` + `&recvMode=${this.recvMode}` + // player参数
      `&enableRecvMessage=${this.enableRecvMessage}`;

      if (!this.roomID) {
        uni.showToast({
          title: '请输入房间号',
          icon: 'none',
          duration: 2000
        });
        return;
      }

      if (!this.userID) {
        uni.showToast({
          title: '请输入用户名',
          icon: 'none',
          duration: 2000
        });
        return;
      }

      const reg = /^[0-9a-zA-Z]*$/;

      if (this.userID.match(reg) === null) {
        uni.showToast({
          icon: 'none',
          title: '用户名为英文加数字'
        });
      } else {
        this.tapTime = nowTime;
        this.checkDeviceAuthorize().then(result => {
          console.log('授权成功', result);
          console.log('navigateTo', url);
          uni.navigateTo({
            url: url
          });
        }).catch(error => {
          console.log('没有授权', error);
        });
      }
    },
    checkDeviceAuthorize: function () {
      this.hasOpenDeviceAuthorizeModal = false;
      return new Promise((resolve, reject) => {
        if (!wx.getSetting || !wx.getSetting()) {
          // 微信测试版 获取授权API异常，目前只能即使没授权也可以通过
          resolve();
        }

        wx.getSetting().then(result => {
          console.log('getSetting', result);
          this.authorizeMic = result.authSetting['scope.record'];
          this.authorizeCamera = result.authSetting['scope.camera'];

          if (result.authSetting['scope.camera'] && result.authSetting['scope.record']) {
            // 授权成功
            resolve();
          } else {
            // 没有授权，弹出授权窗口
            // 注意： wx.authorize 只有首次调用会弹框，之后调用只返回结果，如果没有授权需要自行弹框提示处理
            console.log('getSetting 没有授权，弹出授权窗口', result);
            uni.authorize({
              scope: 'scope.record'
            }).then(res => {
              console.log('authorize mic', res);
              this.authorizeMic = true;

              if (this.authorizeCamera) {
                resolve();
              }
            }).catch(error => {
              console.log('authorize mic error', error);
              this.authorizeMic = false;
            });
            uni.authorize({
              scope: 'scope.camera'
            }).then(res => {
              console.log('authorize camera', res);
              this.authorizeCamera = true;

              if (this.authorizeMic) {
                resolve();
              } else {
                this.openConfirm();
                reject(new Error('authorize fail'));
              }
            }).catch(error => {
              console.log('authorize camera error', error);
              this.authorizeCamera = false;
              this.openConfirm();
              reject(new Error('authorize fail'));
            });
          }
        });
      });
    },
    openConfirm: function () {
      if (this.hasOpenDeviceAuthorizeModal) {
        return;
      }

      this.hasOpenDeviceAuthorizeModal = true;
      return uni.showModal({
        content: '您没有打开麦克风和摄像头的权限，是否去设置打开？',
        confirmText: '确认',
        cancelText: '取消',
        success: res => {
          this.hasOpenDeviceAuthorizeModal = false;
          console.log(res); // 点击“确认”时打开设置页面

          if (res.confirm) {
            console.log('用户点击确认');
            uni.openSetting({
              success: res => {}
            });
          } else {
            console.log('用户点击取消');
          }
        }
      });
    },
    onBack: function () {
      uni.navigateBack({
        delta: 1
      });
    },
    randomUserID: function () {
      this.setData({
        // roomID: parseInt(10000 * Math.random()),
        userID: new Date().getTime().toString(16).split('').reverse().join('')
      });
    }
  }
};
</script>
<style>
.trtc-demo-container {
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
.main{
	width: 100vw;
	flex: 1;
	height: 0px;
}
.scroll-container{
  width: 100%;
	height: 100%;
}
.scroll-content{
	width: 100%;
	display: flex;
	flex-direction: column;
	align-items: center;
}
button.btn {
  display: inline-block;
  width: auto;
  height: 60rpx;
  min-height: 60rpx;
  line-height: 60rpx;
  font-size: 12px;
  font-weight: normal;
  padding: 0 10rpx;
  color: #006eff;
  background-color: #f2f2f2;
  margin: 0 16rpx;
}
button.btn.active{
  color: #f2f2f2;
  background-color: #006eff;
}
button.btn-hover{
  background-color: #d1d1d1;
}
.btn-random{
	position: absolute;
	top: 0;
	right: 0;
	z-index: 99;
}
.trtc-demo-container .title{
  color: #FFFFFF;
  padding-top: 65rpx;
  line-height: 60rpx;
}

.input-box {
  background-color: transparent;
  color: #ffffff;
  padding: 2vw 5vw 1vw;
  border-bottom: 1px solid #577785;
  margin: 10rpx 0 10rpx 0;
  text-align: center;
  box-sizing: border-box;
	width: 80vw;
	position: relative;
}
.input-box input{
	font-size: 20px;
}
.input-box .btn-random{
	margin: 0;
	width: auto;
}
.choice-content {
	margin-top: 20rpx;
	width: 80vw;
	display: flex;
	flex-direction: row;
	justify-content: space-between;
	flex-wrap: wrap;
	font-size: 14px;
	color: #ffffff;
}

.choice-content radio {
	color: #006eff;
}
.choice-content switch {
	color: #006eff;
	transform:scale(0.8);
}

.radio-group-no-box {
	display: inline-block;
	color: #006eff;
	background-color: #ffffff;
	border: 1px solid #006eff;
	border-radius: 4px;
	/* margin-left: 180rpx; */
}
.choice-content .radio-group-no-box .radio-item{
	padding: 3px 8px;
	text-align: center;
	border-right: 1px solid #006eff;
	display: inline-block;
}
.choice-content .radio-group-no-box .radio-item:last-child{
	border-right: none;
}
.choice-content .radio-group-no-box .radio-item.selected{
	color: #ffffff;
	background-color: #006eff;
}
.choice-content .radio-group-no-box radio{
	display: none;
}

.choice-content>label {
	display: flex;
	justify-content: flex-end;
}
.choice-content text {
	display: flex;
	align-items: center;
	justify-content: center;
}
.choice-content .switch-right {
	margin-right: -20rpx;
}
.input-content input{
	text-align: center;
	border-bottom: 1px solid #577785;
}

.input-content.input-1 input{
	width: 60%;
}

.input-content.input-2 input{
	width: 30%;
}
.radio-group-choice {
	display: flex;
	/* justify-content: space-between; */
	justify-content: flex-end;
	/* width: 40vw; */
	/* padding-right: 4.5vw; */
}
.radio-group-choice label:not(:last-child){
	margin-right: 20rpx;
}

.bottom-btn {
	padding: 40rpx 0;
	width: 100vw;
	text-align: center;
}
.bottom-btn .btn-enter{
	width: 80%;
	background-color: #006eff;
	border-radius: 50px;
  color: #ffffff;
}
.close {
  position:absolute;
  padding-left:5vw;
  padding-right:5vw;
  width:50rpx;
  height:60rpx;
}

</style>