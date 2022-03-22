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
          @click="goto(item)"
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
import { loginout } from '@/api/module.js'

export default {
  methods: {
    goto(item) {
      if (item.lable === '退出登陆') {
        uni.showModal({
          title: '提示',
          showCancel: true,
          content: '确认退出吗？',
          success: function (res) {
            if (res.confirm) {
              uni.showLoading({
                title: '正在退出'
              })
              // console.log('退出', res)
              uni.hideLoading()
              uni.clearStorageSync()
              uni.showToast({
                title: '退出成功'
              })
              this.userInfo = {}
              uni.redirectTo({
                url: '/pages/user/index'
              })
            }
          }
        })
      }
    },
    login_() {
      loginout().then((res) => {
        console.log(res)

        let userInfo = {
          login_name: res.data.user.login_name,
          name: res.data.user.name,
          role_id: res.data.user.role_id,
          role_type: res.data.user.role_type,
          sex: res.data.user.sex,
          short_pwd: res.data.user.short_pwd,
          token: res.header.token
        }
        uni.setStorage({
          key: 'userInfo',
          data: userInfo
        })
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

  onLoad() {
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

