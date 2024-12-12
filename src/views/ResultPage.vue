<template>
  <div class="result-page">
    <div class="result-card animate-up">
      <div class="score-circle">
        <el-progress
          type="dashboard"
          :percentage="score"
          :color="scoreColor"
          :width="200"
        >
          <template #default="{ percentage }">
            <span class="score-text">{{ percentage }}%</span>
          </template>
        </el-progress>
      </div>

      <h2>{{ resultTitle }}</h2>
      <p class="result-desc">{{ resultDescription }}</p>

      <div class="category-scores">
        <h3>详细分析</h3>
        <div class="category-grid">
          <div v-for="(score, category) in categoryScores" :key="category" class="category-item">
            <h4>{{ category }}</h4>
            <el-progress 
              :percentage="score"
              :color="getCategoryColor(score)"
              :stroke-width="10"
            />
          </div>
        </div>
      </div>

      <div class="advice-section">
        <h3>关系建议</h3>
        <ul>
          <li v-for="(advice, index) in matchingAdvice" :key="index">
            {{ advice }}
          </li>
        </ul>
      </div>

      <div class="actions">
        <el-button type="primary" @click="retakeTest">重新测试</el-button>
        <el-button type="success" @click="shareResult">分享结果</el-button>
        <el-button type="info" @click="saveResult">保存结果</el-button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()
const router = useRouter()

const score = ref(parseInt(route.query.score) || 0)
const categoryScores = ref(JSON.parse(route.query.categoryScores || '{}'))

// 根据分数计算颜色
const scoreColor = computed(() => {
  if (score.value >= 80) return '#67C23A'
  if (score.value >= 60) return '#E6A23C'
  return '#F56C6C'
})

// 根据分数计算标题
const resultTitle = computed(() => {
  if (score.value >= 80) return '完美匹配！'
  if (score.value >= 60) return '较好契合'
  return '需要努力'
})

// 根据分数给出描述
const resultDescription = computed(() => {
  if (score.value >= 80) {
    return '恭喜！你们的关系非常和谐，彼此有着极高的契合度。你们在沟通、理解和价值观上都有很好的共鸣。'
  }
  if (score.value >= 60) {
    return '你们的关系具有良好的发展基础，虽然可能存在一些小分歧，但通过共同努力完全可以建立更深厚的感情。'
  }
  return '你们的关系还需要更多的交流和理解。不要灰心，这是一个逐步了解和适应的过程。'
})

// 根据分数给出建议
const matchingAdvice = computed(() => {
  if (score.value >= 80) {
    return [
      '继续保持良好的沟通习惯',
      '共同规划未来，制定长期目标',
      '珍惜彼此的信任和理解',
      '适时表达爱意，增进感情'
    ]
  }
  if (score.value >= 60) {
    return [
      '多创造共处的机会，增进了解',
      '学习倾听对方的想法和需求',
      '在分歧时保持开放和包容的态度',
      '寻找并发展共同的兴趣爱好'
    ]
  }
  return [
    '提升沟通的质量和频率',
    '学会换位思考，理解对方的立场',
    '培养共同的兴趣和话题',
    '给予对方更多的关心和支持'
  ]
})

// 重新测试
const retakeTest = () => {
  router.push('/test')
}

// 分享结果
const shareResult = () => {
  // 这里可以添加分享功能
  ElMessage.success('分享功能开发中...')
}

// 获取分类得分的颜色
const getCategoryColor = (score) => {
  if (score >= 80) return '#67C23A'
  if (score >= 60) return '#E6A23C'
  return '#F56C6C'
}

// 保存结果
const saveResult = () => {
  const resultData = {
    date: new Date().toISOString(),
    totalScore: score.value,
    categoryScores: categoryScores.value,
    advice: matchingAdvice.value
  }
  
  // 保存到 localStorage
  const savedResults = JSON.parse(localStorage.getItem('testResults') || '[]')
  savedResults.push(resultData)
  localStorage.setItem('testResults', JSON.stringify(savedResults))
  
  ElMessage.success('结果已保存')
}
</script>

<style scoped>
.result-page {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 40px 20px;
  background: var(--background-color);
}

.result-card {
  max-width: 800px;
  width: 100%;
  background: white;
  border-radius: 24px;
  padding: 40px;
  box-shadow: 0 4px 24px rgba(0, 0, 0, 0.1);
  text-align: center;
}

.score-circle {
  margin: 20px 0 40px;
}

.score-text {
  font-size: 36px;
  font-weight: bold;
}

h2 {
  font-size: 32px;
  margin-bottom: 20px;
  color: var(--primary-color);
}

.result-desc {
  font-size: 18px;
  line-height: 1.6;
  color: var(--secondary-color);
  margin-bottom: 40px;
}

.advice-section {
  text-align: left;
  margin: 40px 0;
  padding: 20px;
  background: #f5f7fa;
  border-radius: 12px;
}

.advice-section h3 {
  font-size: 24px;
  margin-bottom: 20px;
  color: var(--primary-color);
}

.advice-section ul {
  list-style: none;
  padding: 0;
}

.advice-section li {
  margin: 12px 0;
  padding-left: 24px;
  position: relative;
  color: var(--secondary-color);
}

.advice-section li::before {
  content: "•";
  position: absolute;
  left: 0;
  color: var(--primary-color);
}

.actions {
  margin-top: 40px;
  display: flex;
  justify-content: center;
  gap: 20px;
}

.animate-up {
  animation: slideUp 0.8s ease;
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(40px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.category-scores {
  margin: 40px 0;
  padding: 20px;
  background: #f5f7fa;
  border-radius: 12px;
}

.category-scores h3 {
  font-size: 24px;
  margin-bottom: 20px;
  color: var(--primary-color);
}

.category-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 20px;
}

.category-item {
  padding: 15px;
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.05);
}

.category-item h4 {
  margin-bottom: 10px;
  color: var(--primary-color);
}
</style> 