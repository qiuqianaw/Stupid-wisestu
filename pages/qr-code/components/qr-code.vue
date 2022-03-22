<template>
  <view class="qr_code">
    <image :src="qrCode"></image>
  </view>
</template>

<script>
import { queryUserQrCode } from '@/api/module.js'
export default {
  data() {
    return {
      qrCode: '',
      time: null
    }
  },
  mounted() {
    this.queryQrcode()
    this.time = setInterval(this.queryQrcode, 5000)
  },
  methods: {
    queryQrcode() {
      queryUserQrCode().then((res) => {
        this.qrCode = res.qrCode
      })
      this.qrCode = null
    }
  },
  destroyed() {
    clearInterval(this.time)
  }
}
</script>

<style lang="scss">
@import './style/qr-code';
</style>
