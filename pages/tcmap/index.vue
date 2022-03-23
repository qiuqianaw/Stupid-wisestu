 

<template>
  <view class="content">
    <van-button type="default" @click="getMapLocation"
      >getMapLocation</van-button
    >
  </view>
</template>

<script>
export default {
  data() {
    return {}
  },

  methods: {
    getMapLocation() {
      uni.chooseLocation({
        success: (res) => {
          console.log('chooseLocation success', res)
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
  }
}
</script>

<style>
</style>