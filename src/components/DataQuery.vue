<script setup lang="ts">
import { ref } from 'vue'

const query = ref('')
const result = ref('')
const loading = ref(false)

const dataTypes = [
  { value: 'weather', label: '天气' },
  { value: 'stock', label: '股票' },
  { value: 'news', label: '新闻' },
]

const selectedType = ref(dataTypes[0].value)

// 注意：在实际应用中，不应该在前端暴露 API 密钥
const AMAP_API_KEY = 'YOUR_AMAP_API_KEY'

const fetchWeatherData = async (city: string) => {
  try {
    const response = await fetch(`https://restapi.amap.com/v3/weather/weatherInfo?key=${AMAP_API_KEY}&city=${encodeURIComponent(city)}`)
    const data = await response.json()
    if (data.status === '1' && data.lives && data.lives.length > 0) {
      const weatherInfo = data.lives[0]
      return `${city}的天气：${weatherInfo.weather}，温度：${weatherInfo.temperature}°C，湿度：${weatherInfo.humidity}%`
    } else {
      return `抱歉，无法获取${city}的天气信息。`
    }
  } catch (error) {
    console.error('获取天气数据时出错:', error)
    return '抱歉，获取天气数据时出现错误。'
  }
}

const fetchData = async () => {
  loading.value = true
  try {
    if (selectedType.value === 'weather') {
      result.value = await fetchWeatherData(query.value)
    } else {
      // 模拟其他类型的数据查询
      await new Promise(resolve => setTimeout(resolve, 1000))
      result.value = `查询结果: 您查询的${selectedType.value}数据 "${query.value}" 的结果是...`
    }
  } catch (error) {
    console.error('查询数据时出错:', error)
    result.value = '抱歉，查询数据时出现错误。'
  } finally {
    loading.value = false
  }
}
</script>

<template>
  <div class="query-container">
    <select v-model="selectedType">
      <option v-for="type in dataTypes" :key="type.value" :value="type.value">
        {{ type.label }}
      </option>
    </select>
    <input v-model="query" :placeholder="selectedType === 'weather' ? '输入城市名（如：北京、上海、广州）' : '输入查询内容'" @keyup.enter="fetchData" />
    <button @click="fetchData" :disabled="loading">查询</button>
    <div v-if="loading" class="loading">加载中...</div>
    <div v-else-if="result" class="result">{{ result }}</div>
  </div>
</template>

<style scoped>
.query-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
}

select, input, button {
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

input {
  width: 300px;
}

button {
  background-color: #4CAF50;
  color: white;
  cursor: pointer;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #45a049;
}

button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

.loading, .result {
  margin-top: 20px;
  font-size: 18px;
}

.loading {
  color: #666;
}

.result {
  color: #2c3e50;
  font-weight: bold;
}
</style>