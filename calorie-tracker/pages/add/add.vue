<template>
  <view class="container">
    <input v-model="foodName" placeholder="食物名称" />
    <input v-model="kcal" type="number" placeholder="卡路里" />
    <button type="primary" @click="handleSave">保存记录</button>
  </view>
</template>

<script setup>
import { ref } from 'vue'

const foodName = ref('')
const kcal = ref('')

const handleSave = () => {
  console.log('✅ 点击了保存按钮！') // 调试用
  
  if (!foodName.value) {
    uni.showToast({ title: '请输入食物名称', icon: 'none' })
    return
  }

  const today = new Date().toISOString().split('T')[0]
  const record = {
    id: Date.now(),
    name: foodName.value,
    kcal: Number(kcal.value) || 0
  }

  try {
    const key = `diet_${today}`
    const list = uni.getStorageSync(key) || []
    list.push(record)
    uni.setStorageSync(key, list)
    
    uni.showToast({ title: '保存成功！' })
    setTimeout(() => {
      uni.switchTab({ url: '/pages/index/index' })
    }, 1500)
  } catch (e) {
    console.error('保存失败:', e)
    uni.showToast({ title: '保存失败', icon: 'none' })
  }
}
</script>

<style>
.container { padding: 30rpx; }
input { border: 1px solid #ddd; padding: 20rpx; margin-bottom: 20rpx; }
button { margin-top: 30rpx; }
</style>