<template>
<view>
<!-- template grid -->
<template name="grid">
  <view class="template-grid">
    <view class="column-layout">
      <view class="column-1">
        <view class="grid-scroll-container" @touchstart="_handleGridTouchStart" @touchend="_handleGridTouchEnd">
          <!-- <view id="grid-container-id" class="grid-container {{visibleStreamList.length < 4 ? 'stream-' + visibleStreamList.length : visibleStreamList.length%2 == 0? 'stream-odd':'stream-even'}}"> -->
          <view id="grid-container-id" :class="'grid-container ' + (visibleStreamList.length < 4 ? 'stream-' + visibleStreamList.length : 'stream-3')">
            
            <view :class="'view-container pusher-container ' + (pusher.isVisible && ((gridCurrentPage === 1 && gridPlayerPerPage > 3) || gridPlayerPerPage < 4)?'':'none')">
              <live-pusher class="pusher" :url="pusher.url" :mode="pusher.mode" :autopush="pusher.autopush" :enable-camera="pusher.enableCamera" :enable-mic="pusher.enableMic" :muted="!pusher.enableMic" :enable-agc="pusher.enableAgc" :enable-ans="pusher.enableAns" :enable-ear-monitor="pusher.enableEarMonitor" :auto-focus="pusher.enableAutoFocus" :zoom="pusher.enableZoom" :min-bitrate="pusher.minBitrate" :max-bitrate="pusher.maxBitrate" :video-width="pusher.videoWidth" :video-height="pusher.videoHeight" :beauty="pusher.beautyLevel" :whiteness="pusher.whitenessLevel" :orientation="pusher.videoOrientation" :aspect="pusher.videoAspect" :device-position="pusher.frontCamera" :remote-mirror="pusher.enableRemoteMirror" :local-mirror="pusher.localMirror" :background-mute="pusher.enableBackgroundMute" :audio-quality="pusher.audioQuality" :audio-volume-type="pusher.audioVolumeType" :audio-reverb-type="pusher.audioReverbType" :waiting-image="pusher.waitingImage" :debug="debug" :beauty-style="pusher.beautyStyle" :filter="pusher.filter" @statechange="_pusherStateChangeHandler" @netstatus="_pusherNetStatusHandler" @error="_pusherErrorHandler" @bgmstart="_pusherBGMStartHandler" @bgmprogress="_pusherBGMProgressHandler" @bgmcomplete="_pusherBGMCompleteHandler" @audiovolumenotify="_pusherAudioVolumeNotify"></live-pusher>
              <view class="no-video" v-if="!pusher.enableCamera">
                <image class="image" src="/static/components/trtc-room/template/grid/static/mute-camera-white.png"></image>
              </view>
              <!-- <view class="no-audio" wx:if="{{!pusher.enableMic}}">
                <image class="image" src="./static/mute-mic-white.png"></image>
              </view>
              <view class="audio-volume" wx:if="{{pusher.enableMic}}">
                <image class="image" src="./static/micro-open.png"></image>
                <view class="audio-active" style="height:{{pusher.volume}}%">
                  <image class="image" src="./static/audio-active.png"></image>
                </view>
              </view> -->
            </view>

            <view v-for="(item, index) in visibleStreamList" :key="index" :class="'view-container player-container ' + (item.isVisible?'':'none')" :id="'player-'+item.streamID" :data-userid="item.userID" :data-streamtype="item.streamType" @tap="_doubleTabToggleFullscreen">
              <live-player class="player" :id="item.streamID" :data-userid="item.userID" :data-streamid="item.streamID" :data-streamtype="item.streamType" :src="item.src" mode="RTC" :autoplay="item.autoplay" :mute-audio="item.muteAudio" :mute-video="item.muteVideo" :orientation="item.orientation" :object-fit="item.objectFit" :background-mute="item.enableBackgroundMute" :min-cache="item.minCache" :max-cache="item.maxCache" :sound-mode="item.soundMode" :enable-recv-message="item.enableRecvMessage" :auto-pause-if-navigate="item.autoPauseIfNavigate" :auto-pause-if-open-native="item.autoPauseIfOpenNative" :debug="debug" @statechange="_playerStateChange" @fullscreenchange="_playerFullscreenChange" @netstatus="_playerNetStatus" @audiovolumenotify="_playerAudioVolumeNotify"></live-player>
              <view class="no-video" v-if="item.muteVideo">
                <image class="image" src="/static/components/trtc-room/template/grid/static/display-pause-white.png"></image>
                <view class="text">
                  <p>{{item.userID}}</p>
                </view>
              </view>
              <view class="no-video" v-if="!item.hasVideo && !item.muteVideo">
                <image class="image" src="/static/components/trtc-room/template/grid/static/mute-camera-white.png"></image>
                <view class="text">
                  <p>{{item.userID}}</p>
                </view>
                <view class="text">
                  <p>对方摄像头未打开</p>
                </view>
              </view>
              <view class="no-audio" v-if="!item.hasAudio">
                <image class="image" src="/static/components/trtc-room/template/grid/static/mute-mic-white.png"></image>
              </view>
              <view class="audio-volume" v-if="item.hasAudio">
                <image class="image" src="/static/components/trtc-room/template/grid/static/micro-open.png"></image>
                <view class="audio-active" :style="'height:' + item.volume + '%'">
                  <image class="image" src="/static/components/trtc-room/template/grid/static/audio-active.png"></image>
                </view>
              </view>
              <view class="operation-bar">
                <view class="operation-item-container">
                  <view class="operation-item" @tap.stop="_handleSubscribeRemoteAudio" :data-user-i-d="item.userID" :data-stream-type="item.streamType">
                    <image class="item-image" :src="item.muteAudio? './static/speaker-false.png': './static/speaker-white.png'"></image>
                  </view>
                  <view class="operation-item" @tap.stop="_handleSubscribeRemoteVideo" :data-user-i-d="item.userID" :data-stream-type="item.streamType">
                    <image class="item-image" :src="item.muteVideo? './static/display-pause-false.png': './static/display-play-white.png'"></image>
                  </view>
                  <view class="operation-item" @tap="_toggleFullscreen" :data-user-i-d="item.userID" :data-stream-type="item.streamType">
                    <image class="item-image" src="/static/components/trtc-room/template/grid/static/fullscreen-white.png"></image>
                  </view>
                </view>
              </view>
            </view>

            <view v-for="(item, index) in gridPagePlaceholderStreamList" :key="index" class="view-container player-container player-placeholder">
              <image class="image" src="/static/components/trtc-room/template/grid/static/mute-camera-white.png"></image>
            </view>
          </view>
        </view>
      </view>
      <view class="column-2">
        <view class="menu" v-if="!isShowMoreMenu">
          <view class="menu-item" @tap="_switchSettingPanel">
            <image class="image" src="/static/components/trtc-room/template/grid/static/setting-white.png"></image>
          </view>
          <view class="menu-item" @tap="_switchMemberListPanel">
            <image class="image" src="/static/components/trtc-room/template/grid/static/list-white.png"></image>
          </view>
          <view class="menu-item" @tap="_hangUp">
            <image class="image" src="/static/components/trtc-room/template/grid/static/hangup-red.png"></image>
          </view>
          <view class="menu-item" @tap="_toggleIMPanel">
            <image class="image" :src="enableIM? './static/im-white.png': './static/im-disable.png'"></image>
          </view>
        </view>
      </view>
      
    </view>

    <view class="pages-container" v-if="gridPageCount > 1">
      <view v-for="(item, index) in gridPageCount" :key="index" :class="'page-item ' + (index+1 === gridCurrentPage? 'current':'')"></view>
    </view>
    <view :class="'panel memberlist-panel ' + (panelName === 'memberlist-panel' ? '' : 'none')">
      <view @tap="_handleMaskerClick" class="close-btn">X</view>
      <view class="panel-header">成员列表</view>
      <view class="panel-body">
        <view class="panel-tips" v-if="streamList.length === 0">暂无成员</view>
        <scroll-view class="scroll-container" scroll-y="true">
          <view v-for="(item, index) in streamList" :key="index" class="member-item">
            <view class="member-id">{{item.userID}}</view>
            <view class="member-btns">
              <button class="btn" hover-class="btn-hover" :data-userid="item.userID" :data-streamtype="item.streamType" data-key="objectFit" data-value="fillCrop|contain" @tap="_setPlayerProperty">{{item.objectFit === 'fillCrop'? '填充':'适应'}}</button>
              <button class="btn" hover-class="btn-hover" :data-userid="item.userID" :data-streamtype="item.streamType" data-key="orientation" data-value="vertical|horizontal" @tap="_setPlayerProperty">{{item.orientation === 'vertical'? '竖屏':'横屏'}}</button>
              <button class="btn" hover-class="btn-hover" :data-userid="item.userID" :data-streamtype="item.streamType" @tap="_switchStreamType" v-if="item.streamType === 'main'">{{item._definitionType === 'small'? '小画面':'主画面'}}</button>
              <button class="btn" hover-class="btn-hover" :data-userid="item.userID" :data-streamtype="item.streamType" @tap="_handleSnapshotClick">截屏</button>
            </view>
          </view>
        </scroll-view>
      </view>
    </view>
    <view :class="'panel setting-panel ' + (panelName === 'setting-panel' ? '' : 'none')">
      <view @tap="_handleMaskerClick" class="close-btn">X</view>
      <view class="panel-header">推流设置</view>
      <view class="panel-body">
        <scroll-view class="scroll-container" scroll-y="true">
          <view class="setting-option">
            <view class="label">启用摄像头</view>
            <view class="btn-normal" @tap="_toggleVideo">
              <image class="btn-image" :src="pusher.enableCamera? './static/camera-true.png': './static/camera-false.png'"></image>
            </view>
          </view>
          <view class="setting-option">
            <view class="label">启用麦克风</view>
            <view class="btn-normal" @tap="_toggleAudio">
              <image class="btn-image" :src="pusher.enableMic? './static/audio-true.png': './static/audio-false.png'"></image>
            </view>
          </view>
          <view class="setting-option">
            <view class="label">切换摄像头</view>
            <view class="btn-normal" @tap="switchCamera">
              <image class="btn-image" src="/static/components/trtc-room/template/grid/static/switch.png"></image>
            </view>
          </view>
          <view class="setting-option">
            <view class="label">开启美颜</view>
            <switch class="setting-switch" color="#006eff" :checked="pusher.beautyLevel == 9 ? true: false" data-key="beautyLevel" data-value="9|0" data-value-type="number" @change="_setPuserProperty"></switch>
          </view>
          <view class="setting-option">
            <view class="label">开启AGC</view>
            <switch class="setting-switch" color="#006eff" :checked="pusher.enableAgc" data-key="enableAgc" data-value="true" data-value-type="boolean" @change="_setPuserProperty"></switch>
          </view>
          <view class="setting-option">
            <view class="label">开启ANS</view>
            <switch class="setting-switch" color="#006eff" :checked="pusher.enableAns" data-key="enableAns" data-value="true" data-value-type="boolean" @change="_setPuserProperty"></switch>
          </view>
          <view class="setting-option">
            <view class="label">开启横屏推流</view>
            <switch class="setting-switch" color="#006eff" :checked="pusher.videoOrientation === 'vertical' ? false: true" data-key="videoOrientation" data-value="horizontal|vertical" data-value-type="string" @change="_setPuserProperty"></switch>
          </view>
        </scroll-view>
      </view>
    </view>
    <view :class="'panel bgm-panel ' + (panelName === 'bgm-panel' ? '' : 'none')">
      <view @tap="_handleMaskerClick" class="close-btn">X</view>
      <view class="panel-header">背景音乐</view>
      <view class="panel-body">
        <view class="setting-option">
          <view class="label">MIC音量</view>
          <view class="slider-content">
            <slider :value="MICVolume" min="0" max="100" show-value="true" activeColor="#006eff" @change="_changeProperty" data-property-name="MICVolume"></slider>
          </view>
        </view>
        <view class="setting-option">
          <view class="label">BGM音量</view>
          <view class="slider-content">
            <slider :value="BGMVolume" min="0" max="100" show-value="true" activeColor="#006eff" @change="_changeProperty" data-property-name="BGMVolume"></slider>
          </view>
        </view>
        <view class="setting-option">
          <view class="label">播放进度</view>
          <view class="slider-content">
            <progress activeColor="#006eff" :percent="BGMProgress"></progress>
          </view>
        </view>
        <view class="menu">
          <view class="menu-item" @tap="_handleBGMOperation" data-operation-name="playBGM">
            <view class="label">播放</view>
          </view>
          <view class="menu-item" @tap="_handleBGMOperation" data-operation-name="pauseBGM">
            <view class="label">暂停</view>
          </view>
          <view class="menu-item" @tap="_handleBGMOperation" data-operation-name="resumeBGM">
            <view class="label">继续</view>
          </view>
          <view class="menu-item" @tap="_handleBGMOperation" data-operation-name="stopBGM">
            <view class="label">停止</view>
          </view>
        </view>
      </view>
    </view>
    <view :class="'masker ' + (panelName =='' ? 'none' : '')" @tap="_handleMaskerClick"></view>
  </view>
</template>
</view>
</template>

<style>
/* 9人 会议模版 */
.template-grid{
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  /* display: flex;
  flex-direction: row;
  flex-wrap: wrap; */
}
.column-layout{
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
}
.column-layout .column-1{
  flex: 1;
}
.column-layout .column-2{
  position: relative;
  height: 100rpx;
  background-color: rgb(36, 36, 36);
}

.fullscreen-device-fix .column-layout .column-2{
  height: 120rpx;
}
.menu {
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}
.menu .menu-item{
  text-align: center;
  height: 100rpx;
  flex-grow: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.menu .menu-item .image{
  width: 46rpx;
  height: 46rpx;
}
.more-menu {
  position: absolute;
  top: 0;
}
.more-menu .scroll-container{
  width: 100%;
  height: 100rpx;
  white-space: nowrap;
}
.more-menu .menu-item-container{
  width: 20%;
  display: inline-block;
}

.template-grid .grid-container {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
}
.template-grid .grid-scroll-container{
  width: 100%;
  height: 100%;
  /* box-sizing: border-box; */
  /* overflow-y: scroll; */
  background-color: #000;
}
.grid-containe.overflow {
  height: auto;
}
.template-grid .view-container {
  position: relative;
}

.stream-0 .view-container{
   width: 100%;
   height: 100%;
}

.stream-1 .view-container{
   width: 100%;
   height: 50%;
}

.stream-2 .view-container{
   width: 50%;
   height: 50%;
}

.stream-2 .view-container:nth-child(1){
   width: 100%;
   height: 50%;
}

.stream-3 .view-container{
   width: 50%;
   height: 50%;
}

.stream-4 .view-container{
   width: 50%;
   height: 33.3%;
}

.stream-4 .view-container:nth-child(1){
   width: 100%;
   height: 33.3%;
}

.stream-5 .view-container {
   width: 50%;
   height: 33.3%;
}

.stream-6 .view-container{
   width: 33.3%;
   height: 33.3%;
}

.stream-6 .view-container:nth-child(1){
   width: 100%;
   height: 33.3%;
}

.stream-7 .view-container{
   width: 33.3%;
   height: 33.3%;
}

.stream-7 .view-container:nth-child(1){
   width: 50%;
   height: 33.3%;
}

.stream-7 .view-container:nth-child(2){
   width: 50%;
   height: 33.3%;
}

.stream-8 .view-container{
   width: 33.3%;
   height: 33.3%;
}

.stream-even .view-container{
  width: 50%;
  height: 50%;
}

.stream-odd .view-container{
  width: 50%;
  height: 50%;
}
.stream-odd .view-container:last-child{
  width: 100%;
  height: 50%;
}

.template-grid .operation-bar {
  position: absolute;
  bottom: 6rpx;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: center;
}
.operation-bar .operation-item-container {
  width: auto;
  display: flex;
  flex-direction: row;
  justify-content: center;
  background: rgb(0, 0, 0, .3);
  border-radius: 10rpx;
} 
.template-grid .operation-bar .operation-item {
  width: 64rpx;
  height: 64rpx;
  /* flex-grow: 1; */
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.operation-item .item-image{
  width: 36rpx;
  height: 36rpx;
}
.template-grid .volume-progress {
  width: 100%;
  position: absolute;
  bottom: 0;
}

.template-grid .btn-normal {
  width: 64rpx;
  height: 64rpx;
  margin: 0 6rpx;
  box-sizing: border-box;
  display: flex;
  background: rgba(255, 255, 255, 1);
  justify-content: center;
  align-items: center;
  border-radius: 50%;
}

.template-grid .btn-normal .btn-image{
  width: 36rpx;
  height: 36rpx;
}

.template-grid .btn-hangup {
  background: #f75c45;
}

.template-grid .panel{
  position: absolute;
  background: rgba(0, 0, 0, 0.8);
  width: 90vw;
  height: auto;
  z-index: 999;
  top: 50vh;
  left: 50vw;
  transform: translate(-50%, -50%);
  color: white;
  display: flex;
  flex-direction: column;
  padding: 20rpx 0;
  border-radius: 10rpx;
  box-sizing: border-box;
  font-size: 14px;
}
.panel .close-btn {
  position: absolute;
  top: 0;
  right: 0;
  padding: 5px 10px;
}
.panel .panel-header{
  text-align: center;
  padding-bottom: 20rpx;
}
.panel .panel-tips {
  color: #999;
  text-align: center;
}
.panel .panel-body{
  flex: 1;
  max-height: 50vh;
}
.panel .panel-body .scroll-container{
  width: 100%;
  height: 100%;
  box-sizing: border-box;
}

.memberlist-panel .panel-body{
  height: 50vh;
}
.memberlist-panel .member-item {
  display: flex;
  /* border-bottom: 1px solid #999; */
  margin: 16rpx 16rpx 16rpx 32rpx;
}
.memberlist-panel .member-id {
  width: 30%;
  font-size: 12px;
  line-height: 64rpx;
}
.memberlist-panel .member-btns{
  width: 70%;
  display: flex;
  justify-content: flex-end;
}
.memberlist-panel .member-btns .btn-normal{
  margin-left: 0;
}
.memberlist-panel .member-btns .btn{
  margin-right: 0;
}

.setting-panel .panel-body{
  height: 50vh;
}
.setting-panel .setting-option{
  display: flex;
  justify-content: space-between;
  margin: 16rpx 16rpx 16rpx 32rpx;
  /* box-sizing: border-box;
  padding: 12rpx 16rpx 12rpx 32rpx; */
}
.setting-panel .setting-option .label{
  /* line-height: 64rpx; */
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.setting-panel .setting-switch {
  transform:scale(0.8);
  margin-right: -12rpx;
}

.bgm-panel .panel-body{
  height: auto;
}
.bgm-panel .setting-option{
  height: 60rpx;
  display: flex;
  flex-direction: row;
  margin: 16rpx 16rpx 16rpx 32rpx;
}
.bgm-panel .setting-option .label{
  width: 140rpx;
  line-height: 60rpx;
}
.bgm-panel .setting-option .slider-content{
  flex: 1;
  line-height: 60rpx;
}
.bgm-panel .setting-option .slider-content slider{
  transform:scale(0.9);
  margin: 0;
}

.bgm-panel .setting-option .slider-content progress{
  transform:scale(0.9);
  margin-top: 28rpx;
}
.bgm-panel .menu {
  padding: 16rpx 32rpx 16rpx 32rpx;
  box-sizing: border-box;
}
.bgm-panel .menu .menu-item{
  height: 80rpx;
  background-color: #333;
}

.template-grid .masker{
  position: absolute;
  top: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.4);
}

.template-grid .no-stream,
.template-grid .no-video{
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  box-sizing: border-box;
  color:#fff;
  background-color: rgba(0, 0, 0, 0.4);
  font-size: 12px;
}
.template-grid .audio-volume,
.template-grid .no-audio{
  position: absolute;
  bottom: 20rpx;
  left: 20rpx;
  width: 36rpx;
  height: 36rpx;
}

.no-stream .image,
.no-video .image{
  width: 60rpx;
  height: 60rpx;
}

.audio-volume .image,
.no-audio .image{
  width: 36rpx;
  height: 36rpx;
  position: absolute; /*android 的bug ，image absolute后会向上漂移几个像素，如果要对其必须都设置absolute*/
}

.audio-active {
  position: absolute;
  left: 0;
  bottom: 0;
  width: 100%;
  height: 0;
  overflow: hidden;
}
.audio-active .image{
  bottom: 0;
}

.slide-up-tips {
  position: absolute;
  bottom: -100rpx;
  left: 50%;
  transform: translate(-50%, 0);
  width: 200rpx;
  height: auto;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  box-sizing: border-box;
  font-size: 12px;
  color: #fff;
  background-color: rgba(0, 0, 0, 0.4);
  box-sizing: border-box;
  padding: 20rpx;
  border-radius: 10rpx;
  opacity: 0;
}
.slide-up-tips .image {
  width: 100rpx;
  height: 100rpx;
}
.player-placeholder {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.player-placeholder .image {
  width: 100rpx;
  height: 100rpx;
}

.pages-container {
  width: auto;
  left: 50%;
  transform: translate(-50%, 0);
  height: 20rpx;
  position: absolute;
  bottom: 12%;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
}
.pages-container .page-item {
  width: 20rpx;
  height: 20rpx;
  border-radius: 50%;
  margin: 0 8rpx;
  background-color: rgb(99, 99, 99, .5);
}
.pages-container .page-item.current {
  background-color: #fff;
}

.radio-group-no-box {
	display: inline-block;
	color: #006eff;
	background-color: #ffffff;
	border: 1px solid #006eff;
	border-radius: 4px;
  margin-left: 180rpx;
  font-size: 12px;
}
.radio-group-no-box .radio-item{
	padding: 5px 8px;
	text-align: center;
	border-right: 1px solid #006eff;
	display: inline-block;
}
.radio-group-no-box .radio-item:last-child{
	border-right: none;
}
.radio-group-no-box .radio-item.selected{
	color: #ffffff;
	background-color: #006eff;
}
.radio-group-no-box radio{
	display: none;
}

.picker-label{
  display: inline-block;
  color: #006eff;
	background-color: #ffffff;
	border: 1px solid #006eff;
  border-radius: 4px;
  padding: 5px 8px;
  text-align: center;
  font-size: 12px;
}
</style>