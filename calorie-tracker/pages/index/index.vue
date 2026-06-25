<!-- ============================================================ -->
<!-- 这是一个"每日卡路里记录"小程序页面 -->
<!-- 功能：记录每天早餐、午餐、晚餐、加餐的食物和卡路里 -->
<!-- 技术：uni-app 框架（类似 Vue.js 语法） -->
<!-- ============================================================ -->
<template >
  <view class="container">
    <!-- 顶部日期切换 -->
    <view class="header">
      <view class="date-nav">
        <text class="arrow" @click="changeDate(-1)">◀</text>
        <view class="date-info">
          <text class="date-text">{{currentDate}}</text>
          <text class="week-text">{{weekDay}}</text>
        </view>
        <text class="arrow" @click="changeDate(1)">▶</text>
      </view>
    </view>

    <!-- 早餐 -->
    <view class="meal-card">
      <view class="meal-header">
        <view class="title">早餐</view>
        <text class="meal-cal">{{getMealCalorie('B')}} kcal</text> // 计算早餐的总卡路里
      </view>
	  // 已添加的食物列表
      <view class="food-list">
		 <!-- v-for：循环遍历 breakfastList 数组，显示每条食物记录 -->
        <view class="food-item" v-for="item in currentData.breakfastList" :key="item.id">
          <view class="food-info">
            <text class="food-name">{{item.name}}</text>
            <text class="food-cal">{{item.calorie}} kcal</text>
          </view>
          <text class="delete-btn" @click="deleteFood('B', item.id)">✕</text>
        </view>
      </view>
      <button class="add-btn" @click="openPopup('B')">添加记录</button>
    </view>

    <!-- 午餐 -->
    <view class="meal-card">
      <view class="meal-header">
        <view class="title">午餐</view>
        <text class="meal-cal">{{getMealCalorie('L')}} kcal</text>
      </view>
      <view class="food-list">
        <view class="food-item" v-for="item in currentData.lunchList" :key="item.id">
          <view class="food-info">
            <text class="food-name">{{item.name}}</text>
            <text class="food-cal">{{item.calorie}} kcal</text>
          </view>
          <text class="delete-btn" @click="deleteFood('L', item.id)">✕</text>
        </view>
      </view>
      <button class="add-btn" @click="openPopup('L')">添加记录</button>
    </view>

    <!-- 晚餐 -->
    <view class="meal-card">
      <view class="meal-header">
        <view class="title">晚餐</view>
        <text class="meal-cal">{{getMealCalorie('D')}} kcal</text>
      </view>
      <view class="food-list">
        <view class="food-item" v-for="item in currentData.dinnerList" :key="item.id">
          <view class="food-info">
            <text class="food-name">{{item.name}}</text>
            <text class="food-cal">{{item.calorie}} kcal</text>
          </view>
          <text class="delete-btn" @click="deleteFood('D', item.id)">✕</text>
        </view>
      </view>
      <button class="add-btn" @click="openPopup('D')">添加记录</button>
    </view>

    <!-- 加餐 -->
    <view class="meal-card">
      <view class="meal-header">
        <view class="title">加餐</view>
        <text class="meal-cal">{{getMealCalorie('S')}} kcal</text>
      </view>
      <view class="food-list">
        <view class="food-item" v-for="item in currentData.snackList" :key="item.id">
          <view class="food-info">
            <text class="food-name">{{item.name}}</text>
            <text class="food-cal">{{item.calorie}} kcal</text>
          </view>
          <text class="delete-btn" @click="deleteFood('S', item.id)">✕</text>
        </view>
      </view>
      <button class="add-btn" @click="openPopup('S')">添加记录</button>
    </view>

    <!-- 底部占位 -->
	// 因为底部有固定的总卡路里栏，需要留出空间避免内容被遮挡
    <view class="bottom-space"></view>

    <!-- 弹窗 -->
    <view class="mask" v-if="showPopup" @click="closePopup"></view>
	//  弹窗主体：输入食物名称和卡路里
    <view class="popup-box" v-if="showPopup">
		// 弹窗标题，根据当前餐次动态变化
      <view class="popup-title">{{popupTitle}}</view>
      
      <view class="form-item">
        <text class="label">食物名</text>
        <input class="input" v-model="formData.name" placeholder="请输入食物名称" placeholder-class="placeholder"/>
      </view>
      
      <view class="form-item">
        <text class="label">卡路里</text>
        <input class="input" v-model="formData.calorie" placeholder="请输入卡路里 (kcal)" type="number" placeholder-class="placeholder"/>
      </view>

      <view class="btn-group">
        <button class="btn-save" @click="submitForm">保存</button>
        <button class="btn-cancel" @click="closePopup">取消</button>
      </view>
    </view>

    <!-- ========== 底部总卡路里========== -->
    <view class="total-bar">
      <text class="total-label">今日总摄入</text>
      <text class="total-num">{{totalCalorie}} kcal</text>
    </view>

  </view>
</template>

<script>
export default {
  data() {
    return {
      showPopup: false,           // 弹窗显示/隐藏
      currentMealType: '',        // 当前餐次：B早餐 L午餐 D晚餐 S加餐
      formData: { name: '', calorie: '' },  // 弹窗表单数据
      currentDateObj: new Date(), // 当前日期对象
      allData: {}                 // 所有日期的数据
    }
  },
  computed: {
    // 格式化日期为 YYYY-MM-DD
    currentDate() {
      const y = this.currentDateObj.getFullYear();
      const m = String(this.currentDateObj.getMonth() + 1).padStart(2, '0');
      const d = String(this.currentDateObj.getDate()).padStart(2, '0');
      return `${y}-${m}-${d}`;
    },
    // 计算星期几
    weekDay() {
      const days = ['周日', '周一', '周二', '周三', '周四', '周五', '周六'];
      return days[this.currentDateObj.getDay()];
    },
    // 获取当前日期的数据，若不存在则自动创建
    currentData() {
      if (!this.allData[this.currentDate]) {
        this.$set(this.allData, this.currentDate, {
          breakfastList: [],
          lunchList: [],
          dinnerList: [],
          snackList: []
        });
      }
      return this.allData[this.currentDate];
    },
    // 计算今日总卡路里
    totalCalorie() {
      const data = this.currentData;
      let total = 0;
      [...data.breakfastList, ...data.lunchList, ...data.dinnerList, ...data.snackList].forEach(item => {
        total += Number(item.calorie);
      });
      return total;
    },
    // 弹窗标题
    popupTitle() {
      const titles = { 'B': '添加早餐', 'L': '添加午餐', 'D': '添加晚餐', 'S': '添加加餐' };
      return titles[this.currentMealType] || '添加食物';
    }
  },

  onLoad() {
    this.loadData();  // 页面加载时读取本地数据
  },

  methods: {
    // 从本地存储读取历史数据
    loadData() {
      try {
        const saved = uni.getStorageSync('calorieData');
        if (saved) {
          this.allData = JSON.parse(saved);
        }
      } catch (e) {
        console.log('读取数据失败', e);
      }
    },

    // 保存数据到本地存储
    saveData() {
      try {
        uni.setStorageSync('calorieData', JSON.stringify(this.allData));
      } catch (e) {
        console.log('保存数据失败', e);
      }
    },

    // 切换日期的实现
    changeDate(days) {
      const newDate = new Date(this.currentDateObj);
      newDate.setDate(newDate.getDate() + days);
      this.currentDateObj = newDate;
    },

    // 计算某餐次的总卡路里
    getMealCalorie(type) {
      const data = this.currentData;
      let list = [];
      switch(type) {
        case 'B': list = data.breakfastList; break;
        case 'L': list = data.lunchList; break;
        case 'D': list = data.dinnerList; break;
        case 'S': list = data.snackList; break;
      }
      return list.reduce((sum, item) => sum + Number(item.calorie), 0);
    },

    // 打开弹窗
    openPopup(type) {
      this.currentMealType = type;
      this.formData = { name: '', calorie: '' };
      this.showPopup = true;
    },

    // 关闭弹窗
    closePopup() {
      this.showPopup = false;
      this.formData = { name: '', calorie: '' };
    },

    // 提交表单，保存新食物
    submitForm() {
      if (!this.formData.name.trim()) {
        uni.showToast({ title: '请输入食物名称', icon: 'none' });
        return;
      }
      if (!this.formData.calorie) {
        uni.showToast({ title: '请输入卡路里', icon: 'none' });
        return;
      }
      const newFood = {
        id: Date.now(),
        name: this.formData.name.trim(),
        calorie: Number(this.formData.calorie)
      };
      switch (this.currentMealType) {
        case 'B': this.currentData.breakfastList.push(newFood); break;
        case 'L': this.currentData.lunchList.push(newFood); break;
        case 'D': this.currentData.dinnerList.push(newFood); break;
        case 'S': this.currentData.snackList.push(newFood); break;
      }
      this.saveData();
      this.closePopup();
      uni.showToast({ title: '保存成功', icon: 'success' });
    },

    // 删除食物
    deleteFood(type, id) {
      uni.showModal({
        title: '确认删除',
        content: '确定要删除这条记录吗？',
        success: (res) => {
          if (res.confirm) {
            const data = this.currentData;
            let list = [];
            switch(type) {
              case 'B': list = data.breakfastList; break;
              case 'L': list = data.lunchList; break;
              case 'D': list = data.dinnerList; break;
              case 'S': list = data.snackList; break;
            }
            const index = list.findIndex(item => item.id === id);
            if (index > -1) {
              list.splice(index, 1);
              this.saveData();
              uni.showToast({ title: '已删除', icon: 'success' });
            }
          }
        }
      });
    }
  }
}
</script>


<style>

.container { 
  background-color: #eef5ea;
  min-height: 100vh; 
  padding-bottom: 70px; 
}

/* ========== 顶部日期 ========== */
.header { 
  background-color: #b8d4b0;
  padding: 15px 0; 
  margin-bottom: 10px; 

  
}
.date-nav {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 20px;
  padding: 0 20px;
  width: 100%;
  box-sizing: border-box;
}
.arrow {
  font-size: 18px;
  color: #ffffff;
  padding: 5px 10px;
  min-width: 30px;
  text-align: center;
}
.date-info {
  text-align: center;
  min-width: 120px;
}
.date-text { 
  font-size: 18px; 
  font-weight: bold; 
  color: #ffffff; 
  display: block;
}
.week-text {
  font-size: 12px;
  color: #f0f7ed; 
  margin-top: 2px;
}

/* ========== 餐次卡片 ========== */
.meal-card { 
  background-color: #f7faf5;
  margin: 10px; 
  border-radius: 12px; 
  padding: 15px; 
  box-shadow: 0 2px 8px rgba(100, 140, 100, 0.06); 
}
.meal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}
.title { 
  font-size: 16px; 
  font-weight: bold; 
  color: #6a9a6a;
}
.meal-cal {
  font-size: 14px;
  color: #8bc48b;
  font-weight: bold;
}
.food-list { 
  margin-bottom: 10px; 
}
.food-item { 
  display: flex; 
  justify-content: space-between; 
  align-items: center;
  padding: 10px 0; 
  border-bottom: 1px solid #dce8d6;
}
.food-info {
  display: flex;
  flex-direction: column;
  flex: 1;
}
.food-name { 
  font-size: 14px;
  color: #6a9a6a;
}
.food-cal { 
  font-size: 12px;
  color: #8bc48b;
  margin-top: 2px;
}
.delete-btn {
  color: #c8b0b0;
  font-size: 16px;
  padding: 5px 10px;
}
.add-btn { 
  margin-top: 5px; 
  font-size: 14px; 
  background-color: #dce8d6;
  color: #6a9a6a;
  border: none;
  border-radius: 6px;
}

/* ========== 底部占位 ========== */
.bottom-space {
  height: 70px;
}

/* ========== 底部总卡路里 ========== */
.total-bar {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  height: 60px;
  background: linear-gradient(135deg, #b8d4b0 0%, #8bc48b 100%);
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 20px;
  box-shadow: 0 -4px 15px rgba(100, 140, 100, 0.12);
  z-index: 9999;
  border-top: 2px solid #8bc48b;
}
.total-label {
  font-size: 14px;
  color: #f0f7ed;
}
.total-num {
  font-size: 24px;
  font-weight: bold;
  color: #ffffff;
}

/* ========== 弹窗 ========== */
.mask {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(100, 140, 100, 0.25);
  z-index: 998;
}
.popup-box {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #f7faf5;
  border-radius: 16px;
  padding: 25px;
  width: 280px;
  z-index: 999;
  box-shadow: 0 8px 32px rgba(100, 140, 100, 0.15);
}
.popup-title { 
  font-size: 18px; 
  font-weight: bold; 
  text-align: center; 
  margin-bottom: 20px; 
  color: #6a9a6a;
}
.form-item { 
  margin-bottom: 15px; 
}
.label { 
  display: block; 
  font-size: 14px; 
  color: #8bc48b;
  margin-bottom: 8px; 
}
.input { 
  width: 100%; 
  height: 40px; 
  background-color: #eef5ea;
  border: 1px solid #dce8d6;
  border-radius: 8px; 
  padding: 0 12px; 
  font-size: 14px; 
  color: #6a9a6a;
  box-sizing: border-box; 
}
.placeholder { 
  color: #a8c8a8;
}
.btn-group { 
  display: flex; 
  justify-content: center; 
  gap: 15px; 
  margin-top: 20px; 
}
.btn-save { 
  flex: 1; 
  height: 40px; 
  line-height: 40px; 
  background-color: #8bc48b;
  color: #ffffff;
  border-radius: 8px; 
  font-size: 14px; 
  border: none; 
}
.btn-cancel { 
  flex: 1; 
  height: 40px; 
  line-height: 40px; 
  background-color: #dce8d6;
  color: #6a9a6a;
  border-radius: 8px; 
  font-size: 14px; 
  border: none; 
}
</style>