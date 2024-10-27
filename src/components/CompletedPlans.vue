<script setup lang="ts">
interface Plan {
  id: number
  name: string
  completionTime: string
}

defineProps<{
  completedPlans: Plan[]
}>()

const formatDuration = (completionTime: string) => {
  const endTime = new Date(completionTime)
  const duration = endTime.getTime() - Date.now()
  const minutes = Math.round(duration / (1000 * 60))
  return `${minutes}分钟`
}
</script>

<template>
  <div class="completed-plans">
    <h3>已完成计划</h3>
    <div v-if="completedPlans.length === 0" class="no-completed">
      暂无已完成计划
    </div>
    <div v-else class="completed-items">
      <div v-for="plan in completedPlans" :key="plan.id" class="completed-item">
        <h4>{{ plan.name }}</h4>
        <p>计划时长：{{ formatDuration(plan.completionTime) }}</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.completed-plans {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  height: fit-content;
}

.no-completed {
  text-align: center;
  color: #666;
  padding: 20px;
}

.completed-items {
  display: grid;
  gap: 15px;
  margin-top: 15px;
}

.completed-item {
  background-color: #f9f9f9;
  padding: 15px;
  border-radius: 6px;
  border-left: 4px solid #27ae60;
}

.completed-item h4 {
  margin: 0 0 10px 0;
  color: #2c3e50;
  font-size: 0.9em;
}

.completed-item p {
  margin: 5px 0;
  color: #666;
  font-size: 0.8em;
}
</style>