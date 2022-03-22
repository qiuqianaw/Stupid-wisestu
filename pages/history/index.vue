<template>
  <view class="content">
    <view>
      <view v-if="applyList == null">
        <van-empty description="暂无需要填写的信息收集～" />
      </view>
      <detail :applyList="applyList"></detail>
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
    this.test()
  },
  methods: {
    test() {
      getHisApplyList().then((res) => {
        console.log(res)
        if (res.data.result.list.length != 0) {
          this.applyList = res.data.result.list
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
