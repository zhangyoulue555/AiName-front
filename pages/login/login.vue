<!-- login.vue -->
<template>
  <view class="page-container">
    <view class="header-section">
      <view class="logo-area">👋</view>
      <text class="title">欢迎回来</text>
      <text class="subtitle">登录账号，查看您的历史记录</text>
    </view>

    <view class="form-container">
      <view class="input-group">
        <text class="label">邮箱账号</text>
        <input
          v-model="form.email"
          class="xhs-input"
          type="text"
          placeholder="请输入注册邮箱"
          placeholder-class="input-placeholder"
          maxlength="50"
        />
      </view>

      <view class="input-group">
        <text class="label">登录密码</text>
        <input
          v-model="form.password"
          class="xhs-input"
          type="password"
          placeholder="请输入密码"
          placeholder-class="input-placeholder"
          maxlength="20"
        />
      </view>

      <button class="xhs-btn btn-primary" hover-class="btn-hover" @click="onLogin" :disabled="loading">
        {{ loading ? '登录中...' : '立即登录' }}
      </button>

      <view class="footer-links">
        <text class="text-grey">还没有账号？</text>
        <text class="link-highlight" @click="onGotoRegister">立即注册</text>
      </view>
    </view>
  </view>
</template>

<script setup>
import { ref, reactive } from 'vue'
import http from '/http/http.js'

const form = reactive({
  email: '',
  password: ''
})

const loading = ref(false)

const onLogin = async () => {
  if (!form.email.trim()) { uni.showToast({ title: '请输入邮箱', icon: 'none' }); return }
  if (!form.password.trim()) { uni.showToast({ title: '请输入密码', icon: 'none' }); return }
  
  const emailReg = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  if (!emailReg.test(form.email)) { uni.showToast({ title: '邮箱格式不正确', icon: 'none' }); return }

  loading.value = true
  uni.showLoading({ title: '登录中...' })

  try{
    const result = await http.login(form.email, form.password);
    uni.setStorageSync("user", result.user);
    uni.setStorageSync("token", result.token);
    uni.showToast({ title: '登录成功', icon: 'success' });
    setTimeout(() => {
        uni.navigateTo({ url: "/pages/index/index" })
    }, 500)
  }catch(error){
    uni.showToast({ title: error.detail || '登录失败', icon: "none" })
  }finally{
    loading.value = false
    uni.hideLoading()
  }
}

const onGotoRegister = () => {
  uni.navigateTo({ url: '/pages/register/register' })
}
</script>

<style scoped>
/* 复用核心样式变量 */
.page-container {
  min-height: 100vh;
  background-color: #FFF;
  padding: 60rpx 40rpx;
  display: flex;
  flex-direction: column;
}

.header-section {
  margin-top: 80rpx;
  margin-bottom: 80rpx;
}

.logo-area {
  font-size: 80rpx;
  margin-bottom: 20rpx;
}

.title {
  font-size: 56rpx;
  font-weight: 800;
  color: #333;
  display: block;
  margin-bottom: 16rpx;
}

.subtitle {
  font-size: 30rpx;
  color: #999;
}

.input-group {
  margin-bottom: 40rpx;
}

.label {
  display: block;
  font-size: 28rpx;
  color: #333;
  font-weight: 600;
  margin-bottom: 16rpx;
  margin-left: 8rpx;
}

.xhs-input {
  width: 100%;
  height: 100rpx;
  background: #F7F7F7; /* 极浅灰 */
  border-radius: 50rpx;
  padding: 0 40rpx;
  font-size: 32rpx;
  color: #333;
  box-sizing: border-box;
  transition: all 0.2s;
}

.xhs-input:focus {
  background: #FFF;
  border: 2rpx solid #FF2442;
  box-shadow: 0 4rpx 12rpx rgba(255, 36, 66, 0.1);
}

.input-placeholder {
  color: #C0C0C0;
}

.xhs-btn {
  margin-top: 60rpx;
  border-radius: 999px;
  height: 100rpx;
  line-height: 100rpx;
  font-size: 34rpx;
  font-weight: 600;
  border: none;
}
.xhs-btn::after { border: none; }

.btn-primary {
  background: linear-gradient(135deg, #FF2442 0%, #FF5060 100%);
  color: #fff;
  box-shadow: 0 12rpx 30rpx rgba(255, 36, 66, 0.25);
}
.btn-hover { opacity: 0.9; transform: scale(0.98); }

.btn-primary[disabled] {
  opacity: 0.6;
  background: #FFB3B3;
  box-shadow: none;
}

.footer-links {
  margin-top: 40rpx;
  text-align: center;
  font-size: 28rpx;
}

.text-grey { color: #999; }
.link-highlight {
  color: #FF2442;
  font-weight: 600;
  margin-left: 10rpx;
}
</style>