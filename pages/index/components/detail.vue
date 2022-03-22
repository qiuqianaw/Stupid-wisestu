<template>
  <view>
    <view
      class="margin-sm"
      v-for="(item, index) in applyList"
      :key="index"
      @click="gotoDetail(item)"
    >
      <view class="cu-bar bg-white solid-bottom">
        <view class="action">
          <text
            class="cuIcon-title light"
            :class="item.apply_time != null ? 'text-olive' : 'tetxt-grey'"
          ></text>
          {{ item.title }} ( ID: {{ item.apply_id }} )
        </view>
        <view class="text-left">
          <view
            v-if="item.apply_time != null"
            class="cu-tag round margin bg-olive light"
          >
            <text class="text-white text-sm" />已填写
          </view>

          <view v-else class="cu-tag round margin bg-grey light">
            <text class="text-white text-sm" />未填写
          </view>
        </view>
      </view>
      <view class="cu-card dynamic no-card">
        <view class="cu-item shadow padding">
          <view class="text-left text-grey">
            <view><text>表单名称: </text> {{ item.title }}</view>
            <view><text>开始时间: </text>{{ item.begin_time }}</view>
            <view><text>结束时间: </text>{{ item.end_time }}</view>
            <view class="flex justify-start margin-top">
              <view class="text-grey cu-tag round bg-red light" @click="auto_()"
                >获取上次填写数据并设置自动化</view
              >
            </view>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  mounted() {},
  props: {
    applyList: {
      type: Array,
      default: function () {
        return []
      }
    }
  },
  watch: {},
  data() {
    return {}
  },
  methods: {
    auto_() {
      console.log('开发中')
    },
    gotoDetail(item) {
      uni.navigateTo({
        url: '/pages/form/index?data=' + JSON.stringify(item)
      })
    }
  },

  computed: {}
}
</script>

<style lang="scss" scoped>
@import './style/detail.scss';
</style>
