<template>
  <div class="test-page">
    <!-- 进度指示器 -->
    <div class="progress-bar">
      <el-progress :percentage="progress" :format="format" />
    </div>

    <!-- 问题卡片 -->
    <div class="question-card" 
      :class="{ 
        'animate-card': true,
        'slide-left': isNext,
        'slide-right': !isNext 
      }"
    >
      <h2>{{ currentQuestion.title }}</h2>
      <p class="question-desc">{{ currentQuestion.description }}</p>
      
      <div class="options">
        <el-radio-group v-model="currentAnswer" @change="handleAnswer">
          <el-radio 
            v-for="option in currentQuestion.options" 
            :key="option.value" 
            :label="option.value"
            class="option-item"
          >
            {{ option.label }}
          </el-radio>
        </el-radio-group>
      </div>
    </div>

    <!-- 操作按钮 -->
    <div class="actions">
      <el-button 
        v-if="currentIndex > 0" 
        @click="prevQuestion"
        class="action-btn"
      >
        上一题
      </el-button>
      <el-button 
        v-if="currentIndex < questions.length - 1" 
        type="primary" 
        @click="nextQuestion"
        :disabled="!currentAnswer"
        class="action-btn"
      >
        下一题
      </el-button>
      <el-button 
        v-if="currentIndex === questions.length - 1" 
        type="success" 
        @click="submitTest"
        :disabled="!currentAnswer"
        class="action-btn"
      >
        提交测试
      </el-button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

// 测试题目数据
const questions = [
  // 沟通维度
  {
    id: 1,
    category: '沟通方式',
    title: '你们平时的沟通方式是？',
    description: '选择最符合你们日常交流情况的选项',
    options: [
      { label: '经常面对面交流', value: 'A', score: 10 },
      { label: '主要通过电话/视频', value: 'B', score: 8 },
      { label: '以文字消息为主', value: 'C', score: 6 },
      { label: '很少交流', value: 'D', score: 2 }
    ]
  },
  {
    id: 2,
    category: '沟通方式',
    title: '当对方说话时，你们的态度是？',
    description: '选择最符合你们的倾听状态',
    options: [
      { label: '专注倾听并积极回应', value: 'A', score: 10 },
      { label: '基本能听完并回应', value: 'B', score: 8 },
      { label: '经常心不在焉', value: 'C', score: 4 },
      { label: '总是打断对方', value: 'D', score: 2 }
    ]
  },
  // 价值观维度
  {
    id: 3,
    category: '价值观',
    title: '你们对未来的规划有多一致？',
    description: '考虑关于工作、生活等长期目标',
    options: [
      { label: '目标高度一致', value: 'A', score: 10 },
      { label: '大体��向相同', value: 'B', score: 8 },
      { label: '存在一些分歧', value: 'C', score: 5 },
      { label: '完全不同', value: 'D', score: 2 }
    ]
  },
  {
    id: 4,
    category: '价值观',
    title: '对于金钱的态度？',
    description: '考虑双方对待消费和储蓄的观念',
    options: [
      { label: '观念高度一致', value: 'A', score: 10 },
      { label: '略有差异但可接受', value: 'B', score: 8 },
      { label: '存在明显分歧', value: 'C', score: 4 },
      { label: '完全相反', value: 'D', score: 2 }
    ]
  },
  // 情感表达
  {
    id: 5,
    category: '情感表达',
    title: '在对方需要支持时，你们的反应是？',
    description: '选择最符合你们的互动方式',
    options: [
      { label: '立即给予支持和理解', value: 'A', score: 10 },
      { label: '尽可能提供帮助', value: 'B', score: 8 },
      { label: '视情况而定', value: 'C', score: 5 },
      { label: '很少给予回应', value: 'D', score: 2 }
    ]
  },
  {
    id: 6,
    category: '情感表达',
    title: '你们如何表达爱意？',
    description: '选择最符合你们的情感表达方式',
    options: [
      { label: '经常用语言和行动表达', value: 'A', score: 10 },
      { label: '适时表达关心', value: 'B', score: 8 },
      { label: '较少表达但心里在意', value: 'C', score: 5 },
      { label: '很少表达情感', value: 'D', score: 2 }
    ]
  },
  // 生活习惯
  {
    id: 7,
    category: '生活习惯',
    title: '你们的作息时间匹配度？',
    description: '考虑双方的作息规律',
    options: [
      { label: '作息高度一致', value: 'A', score: 10 },
      { label: '略有差异但不影响', value: 'B', score: 8 },
      { label: '差异较大但能协调', value: 'C', score: 5 },
      { label: '完全不同步', value: 'D', score: 2 }
    ]
  },
  {
    id: 8,
    category: '生活习惯',
    title: '对于生活整洁度的要求？',
    description: '考虑双方的生活习惯',
    options: [
      { label: '都很注重整洁', value: 'A', score: 10 },
      { label: '基本保持整洁', value: 'B', score: 8 },
      { label: '要求有所不同', value: 'C', score: 5 },
      { label: '差异很大', value: 'D', score: 2 }
    ]
  },
  // 兴趣爱好
  {
    id: 9,
    category: '兴趣爱好',
    title: '你们共同的兴趣爱好有多少？',
    description: '考虑日常活动和娱乐方式',
    options: [
      { label: '兴趣高度重合', value: 'A', score: 10 },
      { label: '有一些共同爱好', value: 'B', score: 8 },
      { label: '偶尔有交集', value: 'C', score: 5 },
      { label: '几乎没有共同点', value: 'D', score: 2 }
    ]
  },
  {
    id: 10,
    category: '兴趣爱好',
    title: '闲暇时间的安排方式？',
    description: '考虑假期和空闲时间的活动偏好',
    options: [
      { label: '喜欢一起安排活动', value: 'A', score: 10 },
      { label: '经常一起但保留独处时间', value: 'B', score: 8 },
      { label: '各自安排为主', value: 'C', score: 5 },
      { label: '很少一起安排活动', value: 'D', score: 2 }
    ]
  }
]

const currentIndex = ref(0)
const currentAnswer = ref('')
const answers = ref({})

// 计算当前进度
const progress = computed(() => {
  return Math.round((currentIndex.value / questions.length) * 100)
})

// 格式化进度显示
const format = (percentage) => `${percentage}%`

// 获取当前问题
const currentQuestion = computed(() => questions[currentIndex.value])

// 处理答案选择
const handleAnswer = (value) => {
  answers.value[currentQuestion.value.id] = value
}

// 添加动画控制
const isNext = ref(true)

// 修改翻页函数
const nextQuestion = () => {
  isNext.value = true
  if (currentIndex.value < questions.length - 1) {
    currentIndex.value++
    currentAnswer.value = answers.value[questions[currentIndex.value].id] || ''
  }
}

const prevQuestion = () => {
  isNext.value = false
  if (currentIndex.value > 0) {
    currentIndex.value--
    currentAnswer.value = answers.value[questions[currentIndex.value].id] || ''
  }
}

// 计算分数
const calculateScore = () => {
  let totalScore = 0
  let maxPossibleScore = questions.length * 10 // 最高分数

  Object.entries(answers.value).forEach(([questionId, answer]) => {
    const question = questions.find(q => q.id === parseInt(questionId))
    const option = question.options.find(opt => opt.value === answer)
    totalScore += option.score
  })

  const percentage = (totalScore / maxPossibleScore) * 100
  return {
    score: totalScore,
    maxScore: maxPossibleScore,
    percentage: Math.round(percentage)
  }
}

// 添加分类统计功能
const calculateCategoryScores = () => {
  const categoryScores = {}
  const categoryCounts = {}
  
  Object.entries(answers.value).forEach(([questionId, answer]) => {
    const question = questions.find(q => q.id === parseInt(questionId))
    const option = question.options.find(opt => opt.value === answer)
    const category = question.category
    
    if (!categoryScores[category]) {
      categoryScores[category] = 0
      categoryCounts[category] = 0
    }
    
    categoryScores[category] += option.score
    categoryCounts[category]++
  })
  
  // 计算每个类别的平均分
  const categoryResults = {}
  Object.keys(categoryScores).forEach(category => {
    categoryResults[category] = Math.round(
      (categoryScores[category] / (categoryCounts[category] * 10)) * 100
    )
  })
  
  return categoryResults
}

// 自动保存功能
const saveProgress = () => {
  localStorage.setItem('testProgress', JSON.stringify({
    currentIndex: currentIndex.value,
    answers: answers.value
  }))
}

// 加载已保存的进度
const loadProgress = () => {
  const savedProgress = localStorage.getItem('testProgress')
  if (savedProgress) {
    const { currentIndex: savedIndex, answers: savedAnswers } = JSON.parse(savedProgress)
    currentIndex.value = savedIndex
    answers.value = savedAnswers
    currentAnswer.value = answers.value[questions[currentIndex.value].id] || ''
  }
}

// 监听答案变化，自动保存
watch(answers, saveProgress, { deep: true })
watch(currentIndex, saveProgress)

// 组件加载时恢复进度
onMounted(loadProgress)

// 修改提交测试函数
const submitTest = () => {
  const result = calculateScore()
  const categoryScores = calculateCategoryScores()
  
  router.push({
    path: '/result',
    query: { 
      score: result.percentage,
      categoryScores: JSON.stringify(categoryScores)
    }
  })
  localStorage.removeItem('testProgress')
}
</script>

<style scoped>
.test-page {
  max-width: 800px;
  margin: 0 auto;
  padding: 40px 20px;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  gap: 40px;
}

.progress-bar {
  position: sticky;
  top: 20px;
  z-index: 10;
  background: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.1);
}

.question-card {
  background: white;
  padding: 40px;
  border-radius: 16px;
  box-shadow: 0 4px 24px rgba(0, 0, 0, 0.1);
}

.question-card h2 {
  font-size: 24px;
  margin-bottom: 16px;
  color: var(--primary-color);
}

.question-desc {
  color: var(--secondary-color);
  margin-bottom: 32px;
}

.options {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.option-item {
  margin-bottom: 16px;
  padding: 16px;
  border-radius: 8px;
  transition: all 0.3s ease;
}

.option-item:hover {
  background: #f5f7fa;
}

.actions {
  display: flex;
  justify-content: center;
  gap: 20px;
}

.action-btn {
  min-width: 120px;
}

.animate-card {
  animation: fadeIn 0.5s ease;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.slide-left {
  animation: slideLeft 0.5s ease;
}

.slide-right {
  animation: slideRight 0.5s ease;
}

@keyframes slideLeft {
  from {
    opacity: 0;
    transform: translateX(50px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}

@keyframes slideRight {
  from {
    opacity: 0;
    transform: translateX(-50px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}

@media (max-width: 768px) {
  .test-page {
    padding: 20px 10px;
  }

  .question-card {
    padding: 20px;
  }

  .question-card h2 {
    font-size: 20px;
  }

  .option-item {
    padding: 12px;
  }

  .actions {
    flex-direction: column;
    align-items: stretch;
  }

  .action-btn {
    width: 100%;
    margin: 5px 0;
  }
}
</style> 