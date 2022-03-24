<template>
  <view>
    <view class="UCenter-bg">
      <image
        src="https://img.yzcdn.cn/vant/logo.png"
        class="cu-avatar round imager"
        mode="widthFix"
      ></image>
      <view class="text-xl margin-top-sm">{{ userInfo.name }}</view>
      <view class="margin-top-sm">
        <text>{{ userInfo.login_name }}</text>
      </view>

      <van-button
        v-if="userInfo == null"
        type="info"
        size="normal"
        @click="login_()"
        >请登陆</van-button
      >
      <image class="blur_bj" src=""></image>
    </view>
    <view>
      <view
        class="cu-list menu card-menu margin-top-xl margin-bottom-xl radius"
      >
        <!-- 普通 formList，直接循环 -->
        <view
          class="cu-item arrow"
          hover-class="btn-hover"
          v-for="(item, index) in formList"
          :key="index"
          @click="logout()"
        >
          <view class="content">
            <text :class="item.class"></text>
            <text class="text-grey">{{ item.lable }}</text>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  methods: {
    logout() {
      let _this = this
      uni.showModal({
        title: '提示',
        showCancel: true,
        content: '确认退出吗？',
        success: function (res) {
          if (res.confirm) {
            uni.showLoading({
              title: '正在退出'
            })
            uni.hideLoading()
            uni.clearStorageSync()
            uni.showToast({
              title: '退出成功'
            })
            _this.userInfo = null
          }
        }
      })
    },
    login_() {
      uni.navigateTo({
        url: '/pages/login/index'
      })
    }
  },
  data() {
    return {
      userInfo: null,
      formList: [
        {
          url: '/pages/qr-code/index',
          opentype: 'navigateTo',
          lable: '我的二维码（暂未开放）',
          class: 'cuIcon-qr_code text-red'
        },
        {
          url: '',
          opentype: 'method',
          lable: '退出登陆',
          class: 'cuIcon-close text-red'
        }
      ]
    }
  },
  onShow() {
    const userInfo = uni.getStorageSync('userInfo')
    if (!userInfo) {
      uni.showToast({
        icon: 'error',
        title: '请先登录',
        duration: 1000
      })
    }
  },
  onLoad() {
    const userInfo = uni.getStorageSync('userInfo')
    if (userInfo) {
      this.userInfo = userInfo
    }
  },
  onShow() {
    const userInfo = uni.getStorageSync('userInfo')
    if (userInfo) {
      this.userInfo = userInfo
    }
  }
}
</script>

<style lang="scss">
@import './style/index.scss';
</style>

