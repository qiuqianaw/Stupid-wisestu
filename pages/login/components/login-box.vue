<template>
  <view class="login-box">
    <view>
      <van-field
        left-icon="user-o"
        type="number"
        placeholder="请输入账号"
        required
        :value="info.uid"
        :border="false"
        @change="onChangeUid"
      />
      <van-cell :border="false"></van-cell>
      <van-field
        left-icon="eye-o"
        placeholder="请输入密码"
        required
        :value="info.upwd"
        :password="true"
        :border="false"
        @change="onChangeUpwd"
      />
    </view>
    <van-cell :border="false"></van-cell>
    <view>
      <button
        class="cu-btn lg text-center shadow-blur bg-blue"
        @click="onCommit"
      >
        立即登录
      </button>
    </view>
  </view>
</template>
<script>
import { loginout } from '@/api/module.js'
import Toast from '@/wxcomponents/vant/toast/toast.js'

export default {
  data() {
    return {
      info: {
        userid: '',
        userpwd: ''
      }
    }
  },
  methods: {
    onChangeUid(event) {
      this.info.userid = event.detail
    },
    onChangeUpwd(event) {
      this.info.userpwd = event.detail
    },
    onCommit() {
      loginout(this.info).then((res) => {
        console.log(res)
        if (res.data.code != 0) {
          // error
          uni.showToast({
            icon: 'error',
            title: res.data.message,
            duration: 1000
          })
        } else if (res.data.code == 0) {
          // success
          console.log(res.data.message)
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
          uni.showToast({
            title: '登陆成功',
            duration: 1000
          })

          //跳转页面
          let pages = getCurrentPages()
          var delta = 1
          for (var index in pages) {
            if (pages[index].route == 'pages/login/index') {
              delta = pages.length - index
              break
            }
          }
          let second = 1
          const timer = setInterval(() => {
            second -= 1
            clearInterval(timer)
            uni.navigateBack({
              delta: delta
            })
          }, 1000)
        }
      })
    }
  }
}
</script>

<style lang="scss">
@import './style/login-box.scss';
</style>
