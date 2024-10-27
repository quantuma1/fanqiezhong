<script setup lang="ts">
import { ref } from 'vue'

interface NewPlan {
  name: string
  duration: number // Duration in minutes
}

const emit = defineEmits<{
  (event: 'create', plan: { name: string; completionTime: string }): void
}>()

const newPlan = ref<NewPlan>({
  name: '',
  duration: 30
})

const createPlan = () => {
  if (!newPlan.value.name || !newPlan.value.duration) {
    alert('请填写完整的计划信息')
    return
  }

  if (newPlan.value.duration <= 0) {
    alert('时长必须大于0分钟')
    return
  }

  const completionTime = new Date(Date.now() + newPlan.value.duration * 60 * 1000)

  emit('create', {
    name: newPlan.value.name,
    completionTime: completionTime.toISOString()
  })
  
  newPlan.value = {
    name: '',
    duration: 30
  }
}
</script>

<template>
  <div class="plan-form">
    <h2>创建新计划</h2>
    <div class="form-group">
      <input
        v-model="newPlan.name"
        type="text"
        placeholder="计划名称"
        class="input"
        maxlength="50"
      />
      <div class="duration-input">
        <input
          v-model.number="newPlan.duration"
          type="number"
          min="1"
          max="1440"
          class="input"
        />
        <span class="duration-unit">分钟</span>
      </div>
      <button @click="createPlan" class="add-button">
        <span class="plus">+</span> 创建计划
      </button>
    </div>
  </div>
</template>

<style scoped>
.plan-form {
  background-color: #f5f5f5;
  padding: 20px;
  border-radius: 8px;
  margin-bottom: 20px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
}

.form-group {
  display: flex;
  gap: 10px;
  margin-top: 15px;
}

.input {
  padding: 8px 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  transition: border-color 0.3s ease;
}

input[type="text"] {
  flex: 2;
}

.duration-input {
  position: relative;
  display: flex;
  align-items: center;
  flex: 1;
}

.duration-input input {
  width: 100%;
  padding-right: 45px;
}

.duration-unit {
  position: absolute;
  right: 12px;
  color: #666;
  pointer-events: none;
}

.input:focus {
  outline: none;
  border-color: #3498db;
  box-shadow: 0 0 0 2px rgba(52, 152, 219, 0.1);
}

.add-button {
  background-color: #4CAF50;
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 4px;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 5px;
  transition: all 0.3s ease;
  white-space: nowrap;
}

.plus {
  font-size: 18px;
  font-weight: bold;
}

.add-button:hover {
  background-color: #45a049;
  transform: scale(1.05);
}

.add-button:active {
  transform: scale(0.95);
}
</style>