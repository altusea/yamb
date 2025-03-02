<script setup lang="ts">
import { ref, watch } from 'vue'
import axios from 'axios'

const props = defineProps<{
  server: {
    url: string
    apiKey?: string
  }
}>()

const items = ref<any[]>([])
const loading = ref(false)
const error = ref('')

// 获取媒体库内容
async function fetchLibrary() {
  try {
    loading.value = true
    error.value = ''

    const response = await axios.get(`${props.server.url}/Items`, {
      params: {
        Recursive: true,
        IncludeItemTypes: 'Movie,Series',
        SortBy: 'SortName',
        SortOrder: 'Ascending',
        Fields: 'PrimaryImageAspectRatio,BasicSyncInfo',
        ImageTypeLimit: 1,
        EnableImageTypes: 'Primary,Backdrop,Thumb',
        StartIndex: 0,
        Limit: 50
      },
      headers: {
        'X-Emby-Token': props.server.apiKey || ''
      }
    })

    items.value = response.data.Items || []
  } catch (err) {
    error.value = '无法加载媒体库'
    console.error(err)
  } finally {
    loading.value = false
  }
}

// 监听服务器变化
watch(() => props.server, fetchLibrary, { immediate: true })
</script>

<template>
  <div class="catalog-container">
    <!-- 加载状态 -->
    <div v-if="loading" class="loading">加载中...</div>

    <!-- 错误提示 -->
    <div v-if="error" class="error">{{ error }}</div>

    <!-- 媒体库内容 -->
    <div v-if="!loading && !error" class="items-grid">
      <div
        v-for="item in items"
        :key="item.Id"
        class="item-card"
      >
        <img
          v-if="item.ImageTags?.Primary"
          :src="`${props.server.url}/Items/${item.Id}/Images/Primary`"
          :alt="item.Name"
          class="item-image"
        >
        <div class="item-info">
          <h3 class="item-title">{{ item.Name }}</h3>
          <p class="item-year" v-if="item.ProductionYear">
            {{ item.ProductionYear }}
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.catalog-container {
  padding: 20px;
}

.loading,
.error {
  text-align: center;
  padding: 20px;
  color: #666;
}

.items-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 20px;
}

.item-card {
  background: #fff;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  transition: transform 0.2s;
}

.item-card:hover {
  transform: translateY(-4px);
}

.item-image {
  width: 100%;
  aspect-ratio: 2/3;
  object-fit: cover;
}

.item-info {
  padding: 12px;
}

.item-title {
  margin: 0;
  font-size: 16px;
  color: #333;
}

.item-year {
  margin: 4px 0 0;
  font-size: 14px;
  color: #666;
}
</style>
