<template>
  <view>
    <view class="margin-xs">
      <view class="cu-bar bg-white solid-bottom">
        <view class="action">
          <text class="cuIcon-titles text-orange"></text>
          填写表单
        </view>
      </view>
      <view class="cu-card dynamic no-card">
        <view class="cu-item shadow">
          <form class="text-grey">
            <view
              class="cu-form-group"
              v-for="(item, index) in form"
              :key="index"
            >
              <view v-if="item.type == 'R'">
                <radio-group class="block" @change="RadioChange">
                  <view class="cu-form-group margin-top">
                    <view class="title">{{ item.label }}</view>
                    <radio
                      v-for="(v, k) in item.options"
                      :key="k"
                      :class="radio == 'A' ? 'checked' : ''"
                      :checked="radio == 'A' ? true : false"
                      :value="v"
                      >{{ v.label }}</radio
                    >
                  </view>
                </radio-group>
              </view>
              <view v-else>
                <view class="title">
                  {{ item.label }}<text class="text-red">*</text>
                </view>
                <input placeholder="1" name="input" />
              </view>
            </view>

            <view>
              <button
                @click="submitList"
                class="cu-btn bg-blue block lg margin-xl"
              >
                提交
              </button>
            </view>
          </form>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
import { getApplyDetail } from '@/api/module.js'

export default {
  data() {
    return {
      form: [],
      lastForm: []
    }
  },
  watch: {},
  props: {},
  methods: {},
  mounted() {},
  onLoad(option) {
    let _this = this
    let data = JSON.parse(option.data)
    // console.log(data)

    getApplyDetail(data).then((res) => {
      _this.lastForm = res.data.lastInfo
      console.log(' _this.lastForm', _this.lastForm)

      _this.form = res.data.result.info_config
      console.log(' _this.form', _this.form)
    })
  }
}
</script>

<style></style>
