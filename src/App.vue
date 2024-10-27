<script setup lang="ts">
import { ref } from 'vue'
import PlanManager from './components/PlanManager.vue'

const title = ref('计划管理系统')
</script>

<template>
  <div class="container">
    <h1>{{ title }}</h1>
    <PlanManager />
  </div>
</template>

<style scoped>
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

h1 {
  color: #2c3e50;
  margin-bottom: 20px;
  text-align: center;
}
</style>