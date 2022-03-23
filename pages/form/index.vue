<template>
  <view>
    <view class="margin-xs">
      <view class="cu-bar bg-white solid-bottom">
        <view class="action">
          <text class="cuIcon-titles text-orange"></text>
          填写表单
        </view>
      </view>

      <view class="bg-white solid-bottom">
        <view class="bg-white margin-tb-xs padding">
          <rich-text class="" :nodes="otherInfo.detail"></rich-text>
          <view
            ><text
              >填写时间段：{{ otherInfo.begin_time }}至
              {{ otherInfo.end_time }}</text
            ></view
          >
          <view
            ><text>发布单位：{{ otherInfo.dep_name }}</text></view
          >
          <view
            ><text>填写人：{{ otherInfo.student_no }}</text></view
          >
        </view>
      </view>

      <view class="cu-card dynamic no-card">
        <view class="cu-item shadow">
          <van-form>
            <view v-for="(item, index) in form" :key="index">
              <!-- 类型： A(位置) -->

              <view v-if="item.type == 'A'">
                <van-cell
                  required
                  title="当前所在位置(默认为上次填写位置，暂不支持更改)"
                  :label="lastForm[index].value"
                >
                </van-cell>
              </view>

              <!-- 类型： T(输入) -->

              <view v-if="item.type == 'T'">
                <van-cell :required="item.required">
                  <view slot="title">
                    <view class="van-cell-text"
                      >{{ index }}.{{ item.label }}</view
                    >
                    <van-field
                      :value="lastForm[index]"
                      @change="lastForm[index] = $event.mp.detail"
                    />
                  </view>
                </van-cell>
              </view>
              <!-- 类型： R(选择) -->

              <view v-if="item.type == 'R'">
                <van-cell :required="item.required">
                  <view slot="title">
                    <view class="van-cell-text"
                      >{{ index }}.{{ item.label }}</view
                    >
                    <van-radio-group
                      :value="
                        item.isOther == true
                          ? lastForm[index].value
                          : lastForm[index]
                      "
                      @change="
                        item.isOther == true
                          ? lastForm[index].value
                          : (lastForm[index] = $event.mp.detail)
                      "
                      direction="horizontal"
                    >
                      <van-radio
                        v-for="(option, index2) in item.options"
                        :key="index2"
                        :name="option.label"
                        >{{ option.value }}</van-radio
                      >
                    </van-radio-group>
                    <van-field
                      v-if="item.isOther == true"
                      :value="lastForm[index].other"
                      @change="lastForm[index].other = $event.mp.detail"
                      placeholder="无异常情况不用填写"
                    />
                  </view>
                </van-cell>
              </view>
            </view>
            <!-- 类型： 定位 -->
            <view>
              <van-cell required title="定位" :label="location">
                <van-button
                  round
                  icon="location-o"
                  type="warning"
                  @click="getMapLocation"
              /></van-cell>
            </view>
            <view style="margin: 16px">
              <van-button round block type="info" @click="onSubmit"
                >提交</van-button
              >
            </view>
          </van-form>
        </view>
      </view>
    </view>
    <my-popup
      :showDetailInfo="showDetailInfo"
      :show="show"
      @set-show-false="setShowFalse"
    ></my-popup>
  </view>
</template>

<script>
import { getApplyDetail, getProvinceAndCity } from '@/api/module.js'

export default {
  data() {
    return {
      location: '暂未定位',
      form: [],
      lastForm: [],
      show: false,
      otherInfo: {}
    }
  },
  watch: {},
  props: {},
  methods: {
    onSubmit() {
      console.log(this.lastForm)
    },

    getMapLocation() {
      let _this = this
      uni.chooseLocation({
        success: (res) => {
          console.log('chooseLocation success', res)
          _this.location =
            res.name + '(' + res.latitude + ', ' + res.longitude + ')'
          console.log('_this.location', _this.location)
        },
        fail: () => {
          uni.getSetting({
            success: (res) => {
              console.log(res)
              var status = res.authSetting
              if (!status['scope.userLocation']) {
                uni.showModal({
                  title: '是否授权当前位置',
                  content:
                    '需要获取您的地理位置，请确认授权，否则地图功能将无法使用',
                  success: (tip) => {
                    if (tip.confirm) {
                      uni.openSetting({
                        success: (data) => {
                          if (data.authSetting['scope.userLocation'] === true) {
                            uni.showToast({
                              title: '授权成功',
                              icon: 'success',
                              duration: 1000
                            })
                            uni.chooseLocation({
                              success: (res) => {
                                console.log('详细地址', res)
                              }
                            })
                          } else {
                            uni.showToast({
                              title: '授权失败',
                              icon: 'none',
                              duration: 1000
                            })
                          }
                        }
                      })
                    }
                  }
                })
              }
            },
            fail: (res) => {
              uni.showToast({
                title: '调用授权窗口失败',
                icon: 'none',
                duration: 1000
              })
            }
          })
        }
      })
    }
  },
  mounted() {},
  onLoad(option) {
    let _this = this
    let data = JSON.parse(option.data)
    // console.log(data)

    getApplyDetail(data).then((res) => {
      console.log('res', res)
      _this.otherInfo = res.data.result
      console.log('_this.otherInfo', _this.otherInfo)
      _this.lastForm = res.data.lastInfo
      _this.form = res.data.result.info_config
    })
  }
}
</script>

<style></style>
