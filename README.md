# 卡路里追踪器 (Calorie Tracker)
基于 uni-app 的每日饮食记录小程序，支持多平台编译（H5/微信小程序/App）。

## 功能特性
- [x] 按日期记录早餐/午餐/晚餐/加餐
- [x] 左右切换日期查看历史记录
- [x] 添加食物名称和卡路里
- [x] 删除记录（带确认提示）
- [x] 每餐卡路里小计 + 每日总摄入统计
- [x] 数据本地持久化存储

## 技术栈
- uni-app (Vue3)
- 原生 CSS 自定义弹窗组件
- uni.setStorageSync 本地数据持久化


## 运行截图
<div align="center">
  <img width="280" src="https://github.com/user-attachments/assets/e0c12fca-630b-4b5f-aca6-be3e54bf6a44" alt="主界面" />
  <img width="280" src="https://github.com/user-attachments/assets/04ee4c12-93d7-48cd-8a36-cdbac4172e9b" alt="添加食物" />
  <br/>
  <img width="280" src="https://github.com/user-attachments/assets/07d561c1-c15e-4821-ba03-492498cca808" alt="食物列表" />
  <img width="280" src="https://github.com/user-attachments/assets/67355527-7eee-429c-84bf-1627d766f377" alt="日期切换" />
</div>

## 运行方式
```bash
# HBuilderX 导入项目后
npm install
# 运行到浏览器

## 待开发
[ ] 设置每日卡路里目标
[ ] 超出目标提醒
[ ] 数据图表统计
[ ] 食物库/常用食物快捷添加
