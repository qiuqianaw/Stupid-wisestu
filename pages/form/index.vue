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
              <van-cell
                required
                title="定位"
                :label="
                  position.address.length != 0
                    ? position.address +
                      ' (' +
                      position.point.lat +
                      ', ' +
                      position.point.lng +
                      ' )'
                    : '暂无定位'
                "
              >
                <van-button
                  round
                  icon="location-o"
                  type="warning"
                  @click="getMapLocation"
              /></van-cell>
            </view>
            <view style="margin: 16px">
              <van-button
                :disabled="!submitValid"
                round
                block
                type="info"
                @click="onSubmit"
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
import {
  getApplyDetail,
  getProvinceAndCity,
  updateApplyDetail
} from '@/api/module.js'

export default {
  data() {
    return {
      submitValid: true,
      form: [],
      lastForm: [],
      show: false,
      otherInfo: {},
      formData: null,
      position: {
        point: { lng: 0, lat: 0 },
        address: '',
        addressComponents: {
          streetNumber: '',
          street: '',
          district: '',
          city: '',
          province: ''
        }
      }
    }
  },
  watch: {},
  props: {},
  methods: {
    addResolution(add) {
      // let add = '四川省成都市双流区商业街18号'
      let reg = /.+?(省|市|自治区|自治州|县|区)/g
      let result = add.match(reg)
      // 直辖市
      if (result.length == 2) {
        this.position.addressComponents.province = ''
        this.position.addressComponents.city = result[0]
        this.position.addressComponents.district = result[1]
      } else {
        this.position.addressComponents.province = result[0]
        this.position.addressComponents.city = result[1]
        this.position.addressComponents.district = result[2]
      }
    },

    onSubmit() {
      console.log(this.lastForm)
      console.log(this.position)

      let params = {
        apply_id: this.formData.apply_id,
        batch_no: this.formData.batch_no,
        // info_result: JSON.stringify(this.lastForm),
        // apply_location: JSON.stringify(this.position)
        info_result: this.lastForm,
        apply_location: this.position
      }
      updateApplyDetail(params).then((res) => {
        console.log('api ==> ', res)
      })
    },

    getMapLocation() {
      let _this = this
      uni.chooseLocation({
        success: (res) => {
          console.log(res)
          _this.position.address = res.address
          _this.position.point.lng = res.longitude
          _this.position.point.lat = res.latitude
          _this.addResolution(_this.position.address)
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
    this.formData = data

    getApplyDetail(data).then((res) => {
      _this.otherInfo = res.data.result
      _this.lastForm = res.data.lastInfo
      _this.form = res.data.result.info_config
      _this.position = res.data.result.info_result
      console.log('form detail', res)
      if (res.data.result.apply_time) {
        _this.submitValid = false
      }
    })
  }
}
</script>

<style></style>
