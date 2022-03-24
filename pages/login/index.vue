<template>
  <view
    id="index-outer"
    class="bg-gradual-blue padding radius text-center shadow-blur"
  >
    <view class="title">请登录</view>
    <login-box></login-box>
  </view>
</template>

<script>
import loginBox from './components/login-box.vue'
export default {
  components: {
    'login-box': loginBox
  },
  data() {
    return {}
  },
  methods: {},
  onLoad() {}
}
</script>

<style lang="scss">
@import './style/index.scss';
@import '/wxcomponents/colorui/main.css';
@import '/wxcomponents/colorui/icon.css';
</style>
