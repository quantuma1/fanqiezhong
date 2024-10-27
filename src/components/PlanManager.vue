<script setup lang="ts">
import { ref } from 'vue'
import PlanForm from './PlanForm.vue'
import PlanList from './PlanList.vue'
import CompletedPlans from './CompletedPlans.vue'

interface Plan {
  id: number
  name: string
  completionTime: string
  isRunning?: boolean
  remainingTime?: number
}

const plans = ref<Plan[]>([])
const completedPlans = ref<Plan[]>([])

const handleCreatePlan = (newPlan: { name: string; completionTime: string }) => {
  const plan: Plan = {
    id: Date.now(),
    name: newPlan.name,
    completionTime: newPlan.completionTime,
    isRunning: false,
    remainingTime: new Date(newPlan.completionTime).getTime() - Date.now()
  }
  plans.value.push(plan)
}

const handlePlanComplete = (plan: Plan) => {
  plans.value = plans.value.filter(p => p.id !== plan.id)
  completedPlans.value.push(plan)
}

const handlePlanDelete = (planId: number) => {
  plans.value = plans.value.filter(p => p.id !== planId)
}
</script>

<template>
  <div class="plan-manager">
    <div class="main-content">
      <PlanForm @create="handleCreatePlan" />
      <PlanList 
        :plans="plans" 
        @plan-complete="handlePlanComplete"
        @plan-delete="handlePlanDelete"
      />
    </div>
    <CompletedPlans :completed-plans="completedPlans" />
  </div>
</template>

<style scoped>
.plan-manager {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  display: grid;
  grid-template-columns: 1fr 300px;
  gap: 20px;
}

.main-content {
  min-width: 0;
}

@media (max-width: 768px) {
  .plan-manager {
    grid-template-columns: 1fr;
  }
}
</style>