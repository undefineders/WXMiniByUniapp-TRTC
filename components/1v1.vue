<template name="1v1">
  <view class="template-1v1">
    <view v-for="(item, index) in streamList" :key="index" v-if="item.src && (item.hasVideo || item.hasAudio)" :class="'view-container player-container ' + (item.isVisible?'':'none')">
      <live-player class="player" :id="item.streamID" :data-userid="item.userID" :data-streamid="item.streamID" :data-streamtype="item.streamType" :src="item.src" mode="RTC" :autoplay="item.autoplay" :mute-audio="item.muteAudio" :mute-video="item.muteVideo" :orientation="item.orientation" :object-fit="item.objectFit" :background-mute="item.enableBackgroundMute" :min-cache="item.minCache" :max-cache="item.maxCache" :sound-mode="item.soundMode" :enable-recv-message="item.enableRecvMessage" :auto-pause-if-navigate="item.autoPauseIfNavigate" :auto-pause-if-open-native="item.autoPauseIfOpenNative" :debug="debug" @statechange="_playerStateChange" @fullscreenchange="_playerFullscreenChange" @netstatus="_playerNetStatus" @audiovolumenotify="_playerAudioVolumeNotify"></live-player>
    </view>
    <view :class="'view-container pusher-container ' + (pusher.isVisible?'':'none') + ' ' + (streamList.length===0? 'fullscreen':'')">
      <live-pusher class="pusher" :url="pusher.url" :mode="pusher.mode" :autopush="pusher.autopush" :enable-camera="pusher.enableCamera" :enable-mic="pusher.enableMic" :muted="!pusher.enableMic" :enable-agc="pusher.enableAgc" :enable-ans="pusher.enableAns" :enable-ear-monitor="pusher.enableEarMonitor" :auto-focus="pusher.enableAutoFocus" :zoom="pusher.enableZoom" :min-bitrate="pusher.minBitrate" :max-bitrate="pusher.maxBitrate" :video-width="pusher.videoWidth" :video-height="pusher.videoHeight" :beauty="pusher.beautyLevel" :whiteness="pusher.whitenessLevel" :orientation="pusher.videoOrientation" :aspect="pusher.videoAspect" :device-position="pusher.frontCamera" :remote-mirror="pusher.enableRemoteMirror" :local-mirror="pusher.localMirror" :background-mute="pusher.enableBackgroundMute" :audio-quality="pusher.audioQuality" :audio-volume-type="pusher.audioVolumeType" :audio-reverb-type="pusher.audioReverbType" :waiting-image="pusher.waitingImage" :debug="debug" @statechange="_pusherStateChangeHandler" @netstatus="_pusherNetStatusHandler" @error="_pusherErrorHandler" @bgmstart="_pusherBGMStartHandler" @bgmprogress="_pusherBGMProgressHandler" @bgmcomplete="_pusherBGMCompleteHandler" @audiovolumenotify="_pusherAudioVolumeNotify"></live-pusher>
      <view class="loading" v-if="streamList.length === 0">
        <view class="loading-img">
          <image src="/static/components/trtc-room/template/1v1/static/loading.png" class="rotate-img"></image>
        </view>
        <view class="loading-text">等待接听中...</view>
      </view>
    </view>
    <view class="handle-btns">
      <view class="btn-normal" @tap="_toggleAudio">
        <image class="btn-image" :src="(pusher.enableMic? './static/audio-true.png': './static/audio-false.png') + ' '"></image>
      </view>
      <view class="btn-normal" @tap="switchCamera">
        <image class="btn-image" src="/static/components/trtc-room/template/1v1/static/switch.png"></image>
      </view>
      <!-- <view class="btn-normal" bindtap="_toggleVideo">
        <image class="btn-image" src="{{pusher.enableCamera? './static/camera-true.png': './static/camera-false.png'}} "></image>
      </view> -->
      <view class="btn-normal" @tap="_toggleSoundMode">
        <image class="btn-image" :src="(streamList[0].soundMode === 'ear' ? './static/phone.png': './static/speaker-true.png') + ' '"></image>
      </view>
    </view>
    <view class="bottom-btns">
      <view class="btn-normal" data-key="beautyLevel" data-value="9|0" data-value-type="number" @tap="_setPuserProperty">
        <image class="btn-image" :src="(pusher.beautyLevel == 9 ? './static/beauty-true.png': './static/beauty-false.png') + ' '"></image>
      </view>
      <view class="btn-hangup" @tap="_hangUp">
        <image class="btn-image" src="/static/components/trtc-room/template/1v1/static/hangup.png"></image>
      </view>
      <view class="btn-normal" @tap="_toggleIMPanel">
        <image class="btn-image" :src="enableIM? './static/im.png': './static/im-disable.png'"></image>
      </view>
    </view>
  </view>
</template>