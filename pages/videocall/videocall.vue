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
  <button class="btn" @tap="enterRoom" hover-class="none">进入房间</button>
</view>
<cover-image class="close" :style="'top:' + ((headerHeight + statusBarHeight) - 34) + 'rpx'" src="../../static/images/back.png" @tap="onBack"></cover-image>
</view>
</template>

<script>
// index.js
// const app = getApp()
const app = getApp();

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
      headerHeight: app.globalData.headerHeight,
      statusBarHeight: app.globalData.statusBarHeight
    };
  },

  components: {},
  props: {},
  onLoad: function () {},
  methods: {
    enterRoomID: function (event) {
      // console.log('index enterRoomID', event)
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
        uni.showToast({
          title: '请输入房间号',
          icon: 'none',
          duration: 2000
        });
        return;
      }

      if (/^\d*$/.test(roomID) === false) {
        uni.showToast({
          title: '房间号只能为数字',
          icon: 'none',
          duration: 2000
        });
        return;
      }

      if (roomID > 4294967295 || roomID < 1) {
        uni.showToast({
          title: '房间号取值范围为 1~4294967295',
          icon: 'none',
          duration: 2000
        });
        return;
      }

      const url = `../room/room?roomID=${roomID}&template=${this.template}&debugMode=${this.debugMode}&cloudenv=${this.cloudenv}`;
      this.tapTime = nowTime;
      this.checkDeviceAuthorize().then(result => {
        console.log('授权成功', result);
        uni.navigateTo({
          url: url
        });
      }).catch(error => {
        console.log('没有授权', error);
      });
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
    }
  }
};
</script>
<style>
/**index.wxss**/

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
.trtc-demo-container .title{
  color: #FFFFFF;
  padding-top: 65rpx;
  line-height: 60rpx;
}
.trtc-demo-container .input-box {
  background-color: transparent;
  color: #ffffff;
  padding: 2vw 5vw 1vw;
  border-bottom: 1px solid #577785;
  margin: 100rpx 0 40rpx 0;
  text-align: center;
  box-sizing: border-box;
  width: 80vw;
}
.trtc-demo-container .input-box input{
  font-size: 20px;
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

.choice-content switch {
  color: #006eff;
  transform:scale(0.8);
}


.bottom-btn {
  position: fixed;
  width: 100vw;
  text-align: center;
  bottom: 5vh;
}
.bottom-btn .btn{
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