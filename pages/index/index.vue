<template>
  <view class="page-container">
    <view class="header">
      <text class="header-title">✨ 大师取名</text>
      <text class="header-subtitle">赋予宝宝一个美好寓意的名字</text>
    </view>

    <view class="card input-card">
      
      <view class="form-item">
        <text class="section-title">姓氏</text>
        <view class="input-wrapper">
          <input
            v-model="formData.surname"
            class="xhs-input"
            placeholder="请输入姓氏 (如: 李)"
            placeholder-class="input-placeholder"
            maxlength="2"
          />
        </view>
      </view>

      <view class="form-item">
        <text class="section-title">性别</text>
        <view class="tags-group">
          <view
            v-for="item in genderOptions"
            :key="item.value"
            class="xhs-tag"
            :class="{ active: formData.gender === item.value }"
            @click="formData.gender = item.value"
          >
            {{ item.label }}
          </view>
        </view>
      </view>

      <view class="form-item">
        <text class="section-title">名字字数</text>
        <view class="tags-group">
          <view
            v-for="item in lengthOptions"
            :key="item.value"
            class="xhs-tag"
            :class="{ active: formData.length === item.value }"
            @click="formData.length = item.value"
          >
            {{ item.label }}
          </view>
        </view>
      </view>

      <view class="form-item">
        <text class="section-title">更多愿望</text>
        <view class="input-wrapper">
          <textarea
            v-model="formData.other"
            class="xhs-input xhs-textarea"
            placeholder="例如：希望名字里带‘水’字，或者寓意平安健康..."
            placeholder-class="input-placeholder"
            maxlength="100"
            :show-count="false"
          />
        </view>
      </view>

      <view class="btn-group">
        <button class="xhs-btn btn-secondary" hover-class="btn-hover" @click="resetForm">重置</button>
        <button class="xhs-btn btn-primary" hover-class="btn-hover" @click="onGenerateNames">开始起名</button>
      </view>
    </view>

    <view class="result-section" v-if="names.length > 0">
      <view class="section-header">
        <text class="section-heading">精选推荐 · {{ names.length }}个</text>
      </view>
      
      <view class="result-list">
        <view v-for="(item, index) in names" :key="index" class="card result-card">
          <view class="name-badge">
            <text class="name-text">{{ item.name }}</text>
          </view>
          
          <view class="meaning-box">
            <text class="meaning-text">{{ item.moral }}</text>
          </view>
          
          <view class="source-box" v-if="item.reference">
            <text class="icon">📖</text>
            <text class="source-text">{{ item.reference }}</text>
          </view>
        </view>
      </view>

      <button class="xhs-btn btn-outline" hover-class="btn-hover" @click="onReloadNames" :disabled="loading">
        {{ loading ? '灵感生成中...' : '🔄 换一批' }}
      </button>
    </view>

    <view v-else class="empty-state">
      <text class="empty-emoji">👶</text>
      <text class="empty-text">输入信息，开启宝宝的起名之旅</text>
    </view>

    <view style="height: 40rpx;"></view>
  </view>
</template>

<script setup>
import { ref, reactive } from 'vue'
import http from "/http/http.js"

const formData = reactive({
  surname: '',
  gender: "不限",
  length: "不限",
  other: ''
})

let loading = ref(false);

const genderOptions = [
  { label: '不限', value: "不限" },
  { label: '👦 男生', value: "男" },
  { label: '👧 女生', value: "女" }
]

const lengthOptions = [
  { label: '不限', value: "不限" },
  { label: '单字名', value: "单字" },
  { label: '双字名', value: "两字" }
]

const names = ref([])

const resetForm = () => {
  formData.surname = ''
  formData.gender = "不限"
  formData.length = "不限"
  formData.other = ''
  names.value = []
}

const generateNames = async (exclude=[]) => {
	if (!formData.surname.trim()) {
		uni.showToast({ title: '请输入姓氏', icon: 'none' })
		return
	}
	loading.value = true;
	uni.showLoading({ title: '别急，大师正在思考...', mask: true });
	
	try{
		let result = await http.generateName({
			surname: formData.surname,
			gender: formData.gender,
			length: formData.length,
			other: formData.other,
			exclude: exclude
		});
		names.value = result.names;
        // 自动滚动到结果区
        setTimeout(() => {
            uni.pageScrollTo({ scrollTop: 400, duration: 300 })
        }, 100)
	}catch(e){
		uni.showToast({ title: e.message || '网络开小差了', icon: 'none' })
	}finally{
		loading.value = false;
		uni.hideLoading();
	}
}

const onGenerateNames = async () => {
	await generateNames();
}

const onReloadNames = async () => {
	await generateNames(names.value.map((obj) => obj['name']));
}
</script>

<style scoped>
/* 全局容器 */
.page-container {
  min-height: 100vh;
  background-color: #F6F7F9; /* 小红书灰底 */
  padding: 0 32rpx;
  font-family: -apple-system, BlinkMacSystemFont, "PingFang SC", "Helvetica Neue", Arial, sans-serif;
}

/* 头部 */
.header {
  padding: 60rpx 0 40rpx;
  text-align: left;
}
.header-title {
  display: block;
  font-size: 48rpx;
  font-weight: 800;
  color: #333;
  margin-bottom: 12rpx;
  letter-spacing: 1rpx;
}
.header-subtitle {
  font-size: 28rpx;
  color: #999;
  font-weight: 400;
}

/* 卡片通用样式 */
.card {
  background: #fff;
  border-radius: 32rpx; /* 更大的圆角 */
  padding: 40rpx;
  box-shadow: 0 8rpx 24rpx rgba(0, 0, 0, 0.03); /* 极淡的阴影 */
  margin-bottom: 30rpx;
}

/* 表单项 */
.form-item {
  margin-bottom: 40rpx;
}
.section-title {
  display: block;
  font-size: 30rpx;
  font-weight: 600;
  color: #333;
  margin-bottom: 20rpx;
}

/* 输入框：去边框，灰底，胶囊 */
.input-wrapper {
  width: 100%;
}
.xhs-input {
  width: 100%;
  height: 96rpx; /* 加高点击区域 */
  background: #F5F5F5;
  border-radius: 48rpx; /* 胶囊圆角 */
  padding: 0 32rpx;
  font-size: 30rpx;
  color: #333;
  box-sizing: border-box;
  transition: all 0.2s;
}
.xhs-input:focus {
  background: #FFF;
  border: 2rpx solid #FF2442; /* 聚焦高亮 */
  box-shadow: 0 0 0 4rpx rgba(255, 36, 66, 0.1);
}
.xhs-textarea {
  height: auto;
  min-height: 180rpx;
  padding: 24rpx 32rpx;
  border-radius: 32rpx;
  line-height: 1.6;
}
.input-placeholder {
  color: #BBB;
}

/* 标签选择器 (Tags) */
.tags-group {
  display: flex;
  flex-wrap: wrap;
  gap: 20rpx;
}
.xhs-tag {
  padding: 16rpx 40rpx;
  background: #F5F5F5;
  border-radius: 999px;
  font-size: 28rpx;
  color: #666;
  font-weight: 500;
  transition: all 0.2s;
  border: 2rpx solid transparent;
}
.xhs-tag.active {
  background: #FFF0F2; /* 浅红底 */
  color: #FF2442;
  border-color: #FF2442;
  font-weight: 600;
}

/* 按钮体系 */
.btn-group {
  display: flex;
  gap: 24rpx;
  margin-top: 20rpx;
}
.xhs-btn {
  border-radius: 999px;
  height: 96rpx;
  line-height: 96rpx;
  font-size: 32rpx;
  font-weight: 600;
  border: none;
  display: flex;
  align-items: center;
  justify-content: center;
}
.xhs-btn::after { border: none; }
.btn-hover { opacity: 0.9; transform: scale(0.98); }

.btn-primary {
  flex: 2;
  background: linear-gradient(135deg, #FF2442 0%, #FF5060 100%);
  color: #fff;
  box-shadow: 0 10rpx 20rpx rgba(255, 36, 66, 0.2);
}
.btn-secondary {
  flex: 1;
  background: #F5F5F5;
  color: #666;
}
.btn-outline {
  width: 100%;
  background: #fff;
  color: #FF2442;
  border: 2rpx solid #FF2442;
  margin-top: 30rpx;
}

/* 结果列表 */
.section-heading {
  font-size: 32rpx;
  font-weight: 700;
  color: #333;
  margin-left: 10rpx;
  margin-bottom: 24rpx;
  display: block;
}
.result-card {
  padding: 32rpx;
  border-left: 8rpx solid #FF2442;
  display: flex;
  flex-direction: column;
}
.name-badge {
  margin-bottom: 16rpx;
}
.name-text {
  font-size: 44rpx;
  font-weight: 700;
  color: #333;
}
.meaning-text {
  font-size: 28rpx;
  color: #555;
  line-height: 1.6;
  text-align: justify;
}
.source-box {
  margin-top: 20rpx;
  padding-top: 20rpx;
  border-top: 1rpx dashed #eee;
  display: flex;
  align-items: center;
}
.source-text {
  font-size: 24rpx;
  color: #999;
  margin-left: 8rpx;
}

/* 空状态 */
.empty-state {
  text-align: center;
  margin-top: 100rpx;
}
.empty-emoji {
  font-size: 80rpx;
  display: block;
  margin-bottom: 20rpx;
}
.empty-text {
  color: #CCC;
  font-size: 28rpx;
}
</style>