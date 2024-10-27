<script setup lang="ts">
import { ref, onBeforeUnmount } from 'vue'

interface Plan {
  id: number
  name: string
  completionTime: string
  isRunning?: boolean
  remainingTime?: number
}

const props = defineProps<{
  plans: Plan[]
}>()

const emit = defineEmits<{
  (event: 'planComplete', plan: Plan): void
  (event: 'planDelete', planId: number): void
}>()

const timers = ref<{ [key: number]: number }>({})

const formatTime = (ms: number) => {
  if (ms < 0) return '时间已到'
  
  const seconds = Math.floor(ms / 1000)
  const minutes = Math.floor(seconds / 60)
  const hours = Math.floor(minutes / 60)
  
  if (hours > 0) {
    return `${hours}小时${minutes % 60}分钟${seconds % 60}秒`
  }
  if (minutes > 0) {
    return `${minutes}分钟${seconds % 60}秒`
  }
  return `${seconds}秒`
}

const updateRemainingTime = (plan: Plan) => {
  const targetTime = new Date(plan.completionTime).getTime()
  const now = Date.now()
  plan.remainingTime = targetTime - now
  
  if (plan.remainingTime <= 0) {
    clearInterval(timers.value[plan.id])
    plan.isRunning = false
    emit('planComplete', plan)
  }
}

const toggleTimer = (plan: Plan) => {
  if (plan.isRunning) {
    clearInterval(timers.value[plan.id])
    plan.isRunning = false
  } else {
    updateRemainingTime(plan)
    if (plan.remainingTime && plan.remainingTime > 0) {
      plan.isRunning = true
      timers.value[plan.id] = setInterval(() => {
        updateRemainingTime(plan)
      }, 1000)
    }
  }
}

const deletePlan = (plan: Plan) => {
  if (plan.isRunning) {
    clearInterval(timers.value[plan.id])
  }
  emit('planDelete', plan.id)
}

// Cleanup timers when component is destroyed
onBeforeUnmount(() => {
  Object.values(timers.value).forEach(timer => clearInterval(timer))
})
</script>

<template>
  <div class="plans-list">
    <h3>计划列表</h3>
    <div v-if="plans.length === 0" class="no-plans">
      暂无计划
    </div>
    <div v-else class="plan-items">
      <div v-for="plan in plans" :key="plan.id" class="plan-item">
        <div class="plan-content">
          <h4>{{ plan.name }}</h4>
          <p class="countdown" :class="{ 'warning': plan.remainingTime && plan.remainingTime < 60000 }">
            {{ plan.isRunning ? '剩余：' : '计划时长：' }}{{ formatTime(plan.remainingTime || new Date(plan.completionTime).getTime() - Date.now()) }}
          </p>
        </div>
        <div class="plan-actions">
          <button 
            @click="toggleTimer(plan)"
            :class="['timer-button', { 'running': plan.isRunning }]"
          >
            {{ plan.isRunning ? '暂停' : '开始' }}
          </button>
          <button 
            @click="deletePlan(plan)"
            class="delete-button"
            :title="'删除计划'"
          >
            ×
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.plans-list {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.no-plans {
  text-align: center;
  color: #666;
  padding: 20px;
}

.plan-items {
  display: grid;
  gap: 15px;
  margin-top: 15px;
}

.plan-item {
  background-color: #f9f9f9;
  padding: 15px;
  border-radius: 6px;
  border-left: 4px solid #4CAF50;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.plan-content {
  flex: 1;
}

.plan-item h4 {
  margin: 0 0 10px 0;
  color: #2c3e50;
}

.countdown {
  color: #2c3e50;
  font-weight: bold;
  font-size: 1.1em;
}

.countdown.warning {
  color: #e74c3c;
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0% { opacity: 1; }
  50% { opacity: 0.5; }
  100% { opacity: 1; }
}

.plan-actions {
  display: flex;
  gap: 8px;
  align-items: center;
}

.timer-button {
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  background-color: #3498db;
  color: white;
  transition: all 0.3s ease;
  min-width: 80px;
}

.timer-button.running {
  background-color: #e74c3c;
}

.timer-button:hover {
  opacity: 0.9;
  transform: scale(1.05);
}

.timer-button:active {
  transform: scale(0.95);
}

.delete-button {
  width: 32px;
  height: 32px;
  border: none;
  border-radius: 50%;
  background-color: #e74c3c;
  color: white;
  font-size: 20px;
  line-height: 1;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
  padding: 0;
}

.delete-button:hover {
  background-color: #c0392b;
  transform: scale(1.1);
}

.delete-button:active {
  transform: scale(0.9);
}
</style>