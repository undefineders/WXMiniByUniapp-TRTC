<template>
<view>
<view class="container-box">
  <view class="title" :style="'padding-top:' + ((headerHeight + statusBarHeight)/2 - 12) + 'px'">
    <view>语音聊天室</view>
  </view>
  <view class="input-box">
    <input type="number" :value="roomID" maxlength="20" @input="bindRoomID" placeholder="请输入房间号" placeholder-style="color:#ffffff;opacity: 0.55;"></input>
  </view>
  <view class="input-box">
    <input :value="userID" maxlength="20" @input="bindUserID" placeholder="请输入用户名" placeholder-style="color:#ffffff;opacity: 0.55;"></input>
  </view>
  <radio-group class="radio-group" @change="roleChange">
    <label class="radio">
       <radio color="#006eff" value="presenter" checked="true" id="presenter"></radio>主播（可发言）
    </label>
    <label class="radio">
      <radio color="#006eff" value="audience" id="audience"></radio>观众（仅收听）
    </label>
  </radio-group>
  <view class="choice-content">
    <text class="device-position">音量类型</text>
    <radio-group class="radio-group-choice" @change="selectVolumeType">
      <label>
        <radio color="#006eff" value="auto" id="auto"></radio>
        自动
      </label>
      <label>
        <radio color="#006eff" value="media" checked="true" id="media"></radio>
        媒体
      </label>
      <label>
        <radio color="#006eff" value="voicecall" id="voicecall"></radio>
        通话
      </label>
    </radio-group>
	</view>
  <view class="choice-content">
    <label class="label" for="switchDebugMode">
      <text>调试模式</text>
      <switch id="switchDebugMode" color="#006eff" :checked="debugMode" @change="switchDebugMode"></switch>
    </label>
  </view>
</view>
<view class="bottom-btn">
  <button class="btn" @tap="joinRoom" hover-class="none">进入房间</button>
</view>
<cover-image class="close" :style="'top:' + ((headerHeight + statusBarHeight) - 34) + 'rpx'" src="../../../static/images/back.png" @tap="onBack"></cover-image>
</view>
</template>

<script>
const app = getApp();

export default {
  data() {
    return {
      roomID: '',
      userID: '',
      role: 'presenter',
      // 用户角色 presenter audience
      tapTime: '',
      headerHeight: app.globalData.headerHeight,
      statusBarHeight: app.globalData.statusBarHeight,
      lc: '◀︎',
      audioVolumeType: 'media',
      debugMode: false,
      streamList: [],
      debug: ""
    };
  },

  components: {},
  props: {},

  /**
   * 生命周期函数--监听页面加载
   * @param {*} options 配置项
   */
  onLoad: function (options) {},

  /**
   * 生命周期函数--监听页面初次渲染完成
   */
  onReady: function () {},

  /**
   * 生命周期函数--监听页面显示
   */
  onShow: function () {
    uni.setKeepScreenOn({
      keepScreenOn: true
    });
  },

  /**
   * 生命周期函数--监听页面隐藏
   */
  onHide: function () {},

  /**
   * 生命周期函数--监听页面卸载
   */
  onUnload: function () {},
  methods: {
    // 绑定输房间号入框
    bindRoomID: function (e) {
      this.setData({
        roomID: e.detail.value
      });
    },
    bindUserID: function (e) {
      this.setData({
        userID: e.detail.value
      });
    },
    roleChange: function (e) {
      this.setData({
        role: e.detail.value
      });
    },
    selectVolumeType: function (e) {
      this.setData({
        audioVolumeType: e.detail.value
      });
    },
    switchDebugMode: function (event) {
      this.setData({
        debugMode: event.detail.value
      });
    },
    radioDebugChange: function (e) {
      this.setData({
        debug: e.detail.value
      });
    },
    // 进入rtcroom页面
    joinRoom: function () {
      // 防止两次点击操作间隔太快
      const nowTime = new Date();

      if (nowTime - this.tapTime < 1000) {
        return;
      }

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
        return;
      }

      if (/^\d*$/.test(this.roomID) === false) {
        uni.showToast({
          title: '只能为数字',
          icon: 'none',
          duration: 2000
        });
        return;
      }

      const url = '../room/room?&roomID=' + this.roomID + '&debugMode=' + this.debugMode + '&userID=' + this.userID + '&role=' + this.role + '&audioVolumeType=' + this.audioVolumeType;
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
          this.authorizeMic = result.authSetting['scope.record']; // this.authorizeCamera = result.authSetting['scope.camera']

          if (result.authSetting['scope.record']) {
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
              resolve();
            }).catch(error => {
              console.log('authorize mic error', error);
              this.authorizeMic = false;
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
        content: '您没有打开麦克风的权限，是否去设置打开？',
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

.container-box {
  background-image: url("https://mc.qcloudimg.com/static/img/7da57e0050d308e2e1b1e31afbc42929/bg.png");
  background-color: #333;
  background-repeat:no-repeat;
  background-size: cover;
  height: 100vh;
  padding: 0 10vw;
  box-sizing: border-box;
}
.choice-content {
  margin-top: 20rpx;
  width: 100%;
  font-size: 14px;
  color: white;

	display: flex;
	flex-direction: row;
	justify-content: space-between;
	flex-wrap: wrap;
}
.choice-content switch {
  transform: scale(0.8);
}
.radio-group-choice {
	display: flex;
	justify-content: flex-end;
}
.radio-group-choice label:not(:last-child){
	margin-right: 20rpx;
}

.title{
  color: #FFFFFF;
  padding-top: 65rpx;
  line-height: 60rpx;
  text-align: center;
}
.input-box {
  background-color: transparent;
  color: #ffffff;
  padding: 3vw 5vw;
  border-color: #577785;
  border-style: solid;
  border-top-width: 0px;
  border-right-width: 0px;
  border-bottom-width: 1px;
  border-left-width: 0px;
  padding-top: 20%;
  text-align: center;
}
.bottom-btn {
  position: fixed;
  width: 100vw;
  bottom: 5vh;
}
.bottom-btn .btn {
  width: 80%;
  background-color: #006eff;
  border-radius: 50px;
  color: #ffffff;
}

.radio-group {
  margin-top: 8vh;
  display: flex;
  justify-content: space-between;
}
.radio-group .radio {
  font-size: 14px;
  color: #ffffff;
}
.PrevImgBox {
  width:80%;
  height:auto;
  margin:15% 10%;
  align-content:center;
  text-align:center;
}
.PrevImgBox .PrevCell {
  margin:10% 6%;
  width:96%;
  height:auto;
  display:inline;
}
.PrevImgBox .PrevCell>label>image{
  border:2px solid rgba(0,0,0,0);
}
.PrevImgBox .PrevCell>label>image.selected{
  border:2px solid rgba(150,150,250,0.5);
}
.close {
  position:absolute;
  padding-left:5vw;
  padding-right:5vw;
  width:50rpx;
  height:60rpx;
}
</style>