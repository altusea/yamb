<script setup lang="ts">
import { ref } from 'vue'
import EmbyPlayer from './components/EmbyPlayer.vue'

// 模拟多个Emby服务
const embyServers = ref([
  { name: '主服务器', url: 'http://emby1.example.com' },
  { name: '备用服务器', url: 'http://emby2.example.com' }
])

const selectedServer = ref(embyServers.value[0])
</script>

<template>
  <div class="app-container">
    <!-- 侧边栏 -->
    <aside class="sidebar">
      <h2>Emby 服务</h2>
      <ul class="server-list">
        <li
          v-for="server in embyServers"
          :key="server.url"
          :class="{ active: selectedServer.url === server.url }"
          @click="selectedServer = server"
        >
          {{ server.name }}
        </li>
      </ul>
    </aside>

    <!-- 主内容区域 -->
    <main class="main-content">
      <div class="content-wrapper">
        <Catalog
          :server="{
            url: selectedServer.url,
            apiKey: 'your-api-key' // TODO: 需要从配置或用户输入获取
          }"
        />
        <EmbyPlayer
          :server="{
            url: selectedServer.url,
            apiKey: 'your-api-key' // TODO: 需要从配置或用户输入获取
          }"
        />
      </div>
    </main>
  </div>
</template>

<style scoped>
.app-container {
  display: flex;
  height: 100vh;
}

.sidebar {
  width: 250px;
  background-color: #2f2f2f;
  color: #f6f6f6;
  padding: 20px;
}

.server-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.server-list li {
  padding: 10px;
  margin: 5px 0;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.server-list li:hover {
  background-color: #444;
}

.server-list li.active {
  background-color: #396cd8;
}

.main-content {
  flex: 1;
  padding: 20px;
  background-color: #f6f6f6;
  overflow-y: auto;
}
</style>
