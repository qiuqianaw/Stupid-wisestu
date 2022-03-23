<template>
  <van-popup
    :show="show"
    @close="setShowFalse"
    position="bottom"
    round
    closeable
  >
    <view class="block bg-grey light">
      <view class="cu-bar">
        <view class="action">
          <text class="cuIcon-title text-blue"></text>
          更改
        </view>
      </view>
      <form>
        <view
          class="cu-form-group radius"
          v-for="(item, index) in showDetailInfo"
          :key="index"
        >
          <input
            class="text-cut"
            disabled
            :placeholder="
              item.usedate +
              ' | ' +
              item.lesson +
              ' (周' +
              cacul(item.usedate) +
              ')'
            "
          />
        </view>
      </form>
    </view>
  </van-popup>
</template>

<script>
export default {
  props: {
    showDetailInfo: {
      type: Array,
      default: function () {
        return []
      }
    },
    show: {
      type: Boolean,
      default: false
    }
  },
  methods: {
    setShowFalse() {
      this.$emit('set-show-false')
    }
  },
  computed: {}
}
</script>

<style lang="scss" scoped>
// .wrapper {
//   display: flex;
//   align-items: center;
//   justify-content: center;
//   height: 80%;
// }

// .block {
//   width: auto;
//   height: 80%;
// }
</style>
