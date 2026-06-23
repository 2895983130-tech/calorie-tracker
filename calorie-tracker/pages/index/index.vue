<template>
  <view class="page">
    <!-- 日期 -->
    <view class="date-row">
      <button size="mini" @click="offsetDay(-1)">‹</button>
      <text class="date-text">{{ dateStr }}</text>
      <button size="mini" @click="offsetDay(1)">›</button>
    </view>

    <!-- 餐段分组 -->
    <view v-for="g in groups" :key="g.meal" class="card">
      <view class="meal-title">{{ g.meal }} · {{ g.totalKcal }}kcal</view>
      
      <view v-if="!g.items.length" class="empty">暂无记录</view>
      
      <view v-for="item in g.items" :key="item.id" class="row">
        <text>{{ item.name }} ({{ item.amount }})</text>
        <text class="kcal">{{ item.kcal || '-' }}kcal</text>
      </view>
    </view>

    <view class="total">
      今日合计: {{ totalKcal }} kcal
    </view>
  </view>
</template>

<script setup>
import { ref, computed } from 'vue'
import { onShow } from '@dcloudio/uni-app' // 补上这行
// 1. 定义数据
const dateStr = ref('')
const records = ref([])

// 餐段列表
const MEAL_TYPES = ['早餐', '午餐', '晚餐', '加餐']

// 初始化今天日期
const initToday = () => {
  const d = new Date()
  dateStr.value = `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`
}

// 加载数据
const loadRecords = () => {
  try {
    // 这里的 key 要和 add.vue 里存的保持一致
    const key = `diet_${dateStr.value}`
    records.value = uni.getStorageSync(key) || []
  } catch (e) {
    records.value = []
  }
}

// 2. 【重点修复】页面显示时触发
// 注意：这里直接写 onShow()，不要 import，也不要 export
onShow(() => {
  initToday()    // 每次显示都刷新一下日期（防止跨天）
  loadRecords()  // 加载当天的记录
})

// 日期切换
const offsetDay = (days) => {
  const d = new Date(dateStr.value)
  d.setDate(d.getDate() + days)
  dateStr.value = `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`
  loadRecords() // 切日期后重新加载
}

// 计算分组（和之前一样）
const groups = computed(() => {
  return MEAL_TYPES.map(meal => {
    const items = records.value.filter(r => r.meal === meal)
    const totalKcal = items.reduce((sum, r) => sum + (Number(r.kcal) || 0), 0)
    return { meal, items, totalKcal }
  })
})

// 计算总热量
const totalKcal = computed(() => {
  return records.value.reduce((sum, r) => sum + (Number(r.kcal) || 0), 0)
})
</script>

<style>
.page {
  padding: 24rpx;
  background: #f6f6f6;
  min-height: 100vh;
  box-sizing: border-box;
}

.date-row {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 16rpx;
  margin-bottom: 20rpx;
}

.date-text {
  font-size: 34rpx;
  font-weight: 700;
  color: #333;
}

.card {
  background: #ffffff;
  border-radius: 18rpx;
  padding: 22rpx 20rpx;
  margin-bottom: 20rpx;
  box-shadow: 0 2rpx 8rpx rgba(0, 0, 0, 0.04);
}

.meal-title {
  font-size: 28rpx;
  font-weight: 600;
  margin-bottom: 14rpx;
  color: #222;
}

.row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10rpx 0;
  border-bottom: 1rpx solid #f0f0f0;
  font-size: 26rpx;
}

.row:last-child {
  border-bottom: none;
}

.kcal {
  color: #999;
  font-size: 24rpx;
}

.empty {
  color: #bbb;
  font-size: 24rpx;
  text-align: center;
  padding: 20rpx 0;
}

.total {
  text-align: right;
  padding: 20rpx 16rpx 40rpx;
  color: #07c160;
  font-weight: 600;
  font-size: 30rpx;
}
</style>