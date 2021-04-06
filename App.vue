<script>

export default {
  onLaunch: function () {
    const {
      model,
      system,
      statusBarHeight
    } = uni.getSystemInfoSync();
    let headHeight;

    if (/iphone\s{0,}x/i.test(model)) {
      headHeight = 88;
    } else if (system.indexOf('Android') !== -1) {
      headHeight = 68;
    } else {
      headHeight = 64;
    }

    this.globalData.headerHeight = headHeight;
    this.globalData.statusBarHeight = statusBarHeight;
  },
  globalData: {
    headerHeight: 0,
    statusBarHeight: 0
  },
  methods: {}
};
</script>
<style>
/**app.wxss**/
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  box-sizing: border-box;
} 

button {
  background: initial;
}

button:focus{
  outline: 0;
}

button::after{
  border: none;
}


page {
  background: #f6f6f6;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}

</style>