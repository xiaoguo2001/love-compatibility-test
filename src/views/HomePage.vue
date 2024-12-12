<template>
  <div class="home">
    <div class="hero">
      <h1 class="animate-title">AI 恋爱契合度测试</h1>
      <p class="animate-text">探索你们之间的缘分密码</p>
      <el-button class="start-button animate-button" @click="$router.push('/test')">
        开始测试
        <el-icon class="arrow"><ArrowRight /></el-icon>
      </el-button>
    </div>
    
    <div class="features">
      <div class="feature-item animate-fade">
        <el-icon size="48"><Star /></el-icon>
        <h3>AI 智能分析</h3>
        <p>采用先进算法，精准评估契合度</p>
      </div>
      <div class="feature-item animate-fade">
        <el-icon size="48"><Histogram /></el-icon>
        <h3>科学测评</h3>
        <p>基于心理学原理，深入分析关系</p>
      </div>
      <div class="feature-item animate-fade">
        <el-icon size="48"><Key /></el-icon>
        <h3>隐私保护</h3>
        <p>数据安全加密，保护个人隐私</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ArrowRight, Star, Histogram, Key } from '@element-plus/icons-vue'
</script>

<style scoped>
.home {
  min-height: 100vh;
  background: var(--background-color);
}

.hero {
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  padding: 0 20px;
}

.start-button {
  margin-top: 40px;
  height: 50px;
  padding: 0 40px;
  font-size: 18px;
  border-radius: 25px;
  background: var(--primary-color);
  color: white;
  border: none;
  display: flex;
  align-items: center;
  gap: 8px;
  transition: transform 0.3s ease;
}

.start-button:hover {
  transform: scale(1.05);
  background: #333;
}

.features {
  padding: 100px 20px;
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 40px;
  max-width: 1200px;
  margin: 0 auto;
}

.feature-item {
  text-align: center;
  padding: 40px;
}

.feature-item h3 {
  margin: 20px 0;
  font-size: 24px;
}

.feature-item p {
  font-size: 16px;
  color: var(--secondary-color);
}

/* 动画效果 */
.animate-title {
  animation: slideUp 1s ease;
}

.animate-text {
  animation: slideUp 1s ease 0.2s both;
}

.animate-button {
  animation: slideUp 1s ease 0.4s both;
}

.animate-fade {
  animation: fadeIn 1s ease 0.6s both;
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
</style> 