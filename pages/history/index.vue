<template>
  <view class="content">
    <view>
      <view v-if="applyList == null">
        <van-empty description="暂无需要填写的信息收集，请刷新试试～" />
      </view>
      <detail v-if="applyList != null" :applyList="applyList"></detail>
    </view>
  </view>
</template>

<script>
import { getHisApplyList } from '@/api/module.js'
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
  methods: {
    test() {
      getHisApplyList().then((res) => {
        if (res.data.result.list.length != 0) {
          this.applyList = res.data.result.list
          this.applyList = this.applyList.filter((elem) => {
            return elem.title == '每日填报'
          })
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
