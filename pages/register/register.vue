<!-- register.vue -->
<template>
  <view class="page-container">
    <view class="header-section">
      <text class="title">创建账号</text>
      <text class="subtitle">开启您的宝宝取名之旅</text>
    </view>

    <view class="form-container">
      <view class="input-group">
        <text class="label">用户名</text>
        <input
          v-model="form.username"
          class="xhs-input"
          type="text"
          placeholder="2-12位中英文/数字"
          placeholder-class="input-placeholder"
          maxlength="12"
        />
      </view>

      <view class="input-group">
        <text class="label">邮箱地址</text>
        <input
          v-model="form.email"
          class="xhs-input"
          type="text"
          placeholder="用于接收验证码"
          placeholder-class="input-placeholder"
          maxlength="50"
        />
      </view>

      <view class="input-group">
        <text class="label">验证码</text>
        <view class="code-wrapper">
          <input
            v-model="form.code"
            class="xhs-input code-input"
            type="number"
            placeholder="6位数字"
            placeholder-class="input-placeholder"
            maxlength="6"
          />
          <button
            class="code-btn"
            :class="{ 'sending': countdown > 0 }"
            @click="onSendCode"
            :disabled="countdown > 0"
          >
            {{ countdown > 0 ? `${countdown}s` : '获取验证码' }}
          </button>
        </view>
      </view>

      <view class="input-group">
        <text class="label">设置密码</text>
        <input
          v-model="form.password"
          class="xhs-input"
          type="password"
          placeholder="6-20位字符"
          placeholder-class="input-placeholder"
          maxlength="20"
        />
      </view>

      <view class="input-group">
        <text class="label">确认密码</text>
        <input
          v-model="form.confirmPassword"
          class="xhs-input"
          type="password"
          placeholder="再次输入密码"
          placeholder-class="input-placeholder"
          maxlength="20"
        />
      </view>

      <button class="xhs-btn btn-primary" hover-class="btn-hover" @click="onRegister" :disabled="loading">
        {{ loading ? '注册中...' : '立即注册' }}
      </button>

      <view class="footer-links">
        <text class="text-grey">已有账号？</text>
        <text class="link-highlight" @click="onGotoLogin">去登录</text>
      </view>
    </view>
  </view>
</template>

<script setup>
import { ref, reactive } from 'vue'
import http from '/http/http.js'

const form = reactive({
  username: '',
  email: '',
  code: '',
  password: '',
  confirmPassword: ''
})

const loading = ref(false)
const countdown = ref(0)

const onSendCode = async() => {
  if (!form.email.trim()) { uni.showToast({ title: '请输入邮箱', icon: 'none' }); return }
  const emailReg = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  if (!emailReg.test(form.email)) { uni.showToast({ title: '邮箱格式不正确', icon: 'none' }); return }

  uni.showToast({ title: '发送中...', icon: 'loading' })
  
  // 模拟倒计时
  countdown.value = 60
  const timer = setInterval(() => {
    if (countdown.value > 0) { countdown.value-- } else { clearInterval(timer) }
  }, 1000)
  
  try {
     let result = await http.getEmailCode(form.email)
     uni.showToast({ title: '验证码已发送', icon: 'success' })
  } catch(e) {
     console.error(e)
  }
}

const onRegister = async () => {
  if (!form.username.trim() || form.username.length < 2) { uni.showToast({ title: '用户名至少2位', icon: 'none' }); return }
  if (!form.email.trim()) { uni.showToast({ title: '请输入邮箱', icon: 'none' }); return }
  if (!form.code.trim()) { uni.showToast({ title: '请输入验证码', icon: 'none' }); return }
  if (!form.password.trim() || form.password.length < 6) { uni.showToast({ title: '密码至少6位', icon: 'none' }); return }
  if (form.password !== form.confirmPassword) { uni.showToast({ title: '两次密码不一致', icon: 'none' }); return }

  loading.value = true
  uni.showLoading({ title: '注册中...' })

  try{
	  await http.register({
	  	  "username": form.username,
	  	  "email": form.email, 
	  	  "password": form.password, 
	  	  "confirm_password": form.confirmPassword,
	  	  "code": form.code
	  })
	  uni.showToast({ title: '注册成功！', icon: 'success' })
      setTimeout(() => {
          uni.redirectTo({ url: '/pages/login/login' })
      }, 1000)
  }catch(error){
	  if(error.statusCode != 200){
	  	  uni.showToast({ title: error.message || '注册失败', icon: "none" })
	  }
  }finally{
	loading.value = false
	uni.hideLoading();
  }
}

const onGotoLogin = () => {
  uni.redirectTo({ url: '/pages/login/login' })
}
</script>

<style scoped>
/* 继承 Login 页面的核心样式 */
.page-container {
  min-height: 100vh;
  background-color: #FFF;
  padding: 40rpx 40rpx;
}
.header-section { margin-top: 40rpx; margin-bottom: 60rpx; }
.title { font-size: 56rpx; font-weight: 800; color: #333; margin-bottom: 16rpx; display: block;}
.subtitle { font-size: 30rpx; color: #999; }
.input-group { margin-bottom: 36rpx; }
.label { display: block; font-size: 28rpx; color: #333; font-weight: 600; margin-bottom: 16rpx; margin-left: 8rpx; }
.xhs-input { width: 100%; height: 100rpx; background: #F7F7F7; border-radius: 50rpx; padding: 0 40rpx; font-size: 32rpx; color: #333; transition: all 0.2s; box-sizing: border-box;}
.xhs-input:focus { background: #FFF; border: 2rpx solid #FF2442; }
.input-placeholder { color: #C0C0C0; }

/* 验证码特殊样式 */
.code-wrapper {
  position: relative;
  width: 100%;
  display: flex;
  align-items: center;
}
.code-input {
  padding-right: 200rpx; /* 为按钮留出位置 */
}
.code-btn {
  position: absolute;
  right: 12rpx;
  top: 10rpx;
  bottom: 10rpx;
  height: auto;
  line-height: 80rpx;
  background: #fff;
  color: #FF2442;
  font-size: 26rpx;
  font-weight: 600;
  border-radius: 40rpx;
  padding: 0 24rpx;
  border: none;
  z-index: 10;
  box-shadow: 0 2rpx 8rpx rgba(0,0,0,0.05);
}
.code-btn.sending {
  color: #999;
  background: #f0f0f0;
}
.code-btn::after { border: none; }

/* 按钮与底部 */
.xhs-btn {
  margin-top: 50rpx;
  border-radius: 999px;
  height: 100rpx;
  line-height: 100rpx;
  font-size: 34rpx;
  font-weight: 600;
  background: linear-gradient(135deg, #FF2442 0%, #FF5060 100%);
  color: #fff;
  border: none;
  box-shadow: 0 12rpx 30rpx rgba(255, 36, 66, 0.25);
}
.xhs-btn::after { border: none; }
.btn-hover { opacity: 0.9; transform: scale(0.98); }
.footer-links { margin-top: 40rpx; text-align: center; font-size: 28rpx; padding-bottom: 40rpx;}
.text-grey { color: #999; }
.link-highlight { color: #FF2442; font-weight: 600; margin-left: 10rpx; }
</style>