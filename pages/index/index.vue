<template>
  <view class="content">
    <view class="margin text-left text-shadow text-grey"
      >主要功能：1表单自动化填写提交 2模拟定位。<br />使用教程：首次填写需在智慧学工完成，再回到本程序设置自动化。当表单有变动时，需回到智慧学工重新进行首次填写。</view
    >
    <view>
      <view v-if="applyList == null">
        <van-empty description="暂无需要填写的信息收集，请刷新试试～" />
      </view>
      <detail v-if="applyList != null" :applyList="applyList"></detail>
    </view>
  </view>
</template>

<script>
import { getApplyList } from '@/api/module.js'
import detail from './components/detail.vue'

export default {
  components: {
    detail: detail
  },
  data() {
    return {
      applyList: null
    }
  },
  onShow() {
    const userInfo = uni.getStorageSync('userInfo')
    if (!userInfo) {
      this.applyList = null
      uni.showToast({
        icon: 'error',
        title: '请先登录',
        duration: 1000
      })
    } else {
      if (!this.applyList) {
        this.test()
      }
    }
  },
  onLoad() {
    const userInfo = uni.getStorageSync('userInfo')
    if (userInfo) {
      this.test()
    } else {
      uni.showToast({
        icon: 'error',
        title: '请先登录',
        duration: 1000
      })
    }
  },
  methods: {
    test() {
      let _this = this
      getApplyList().then((res) => {
        if (res.data.result.list.length != 0) {
          _this.applyList = res.data.result.list
        }
      })
    }
  },
  onPullDownRefresh() {
    const _this = this
    // console.log('refresh')
    setTimeout(function () {
      _this.test()
      uni.stopPullDownRefresh()
    }, 100)
  }
}
</script>

<style>
</style>
