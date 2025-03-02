<template>
  <div class="player-container">
    <video ref="videoPlayer" controls></video>
    <div class="controls">
      <button @click="play">播放</button>
      <button @click="pause">暂停</button>
      <input type="range" v-model="volume" min="0" max="1" step="0.1">
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import axios from 'axios'

const videoPlayer = ref<HTMLVideoElement | null>(null)
const volume = ref(1)
const mediaSource = ref('')
const error = ref('')

// 使用占位符URL和API密钥
const EMBY_SERVER = 'http://your-embyserver.com'
const EMBY_API_KEY = 'your-api-key'

async function loadMedia(itemId: string) {
  try {
    const response = await axios.get(`${EMBY_SERVER}/Items/${itemId}/PlaybackInfo`, {
      headers: {
        'X-Emby-Token': EMBY_API_KEY
      }
    })

    if (response.data.MediaSources?.length > 0) {
      mediaSource.value = response.data.MediaSources[0].Path
    }
  } catch (err) {
    error.value = '无法加载媒体'
    console.error(err)
  }
}

function play() {
  if (videoPlayer.value) {
    videoPlayer.value.src = mediaSource.value
    videoPlayer.value.play()
  }
}

function pause() {
  videoPlayer.value?.pause()
}

// 示例：加载一个媒体项
onMounted(() => {
  loadMedia('your-item-id')
})
</script>

<style scoped>
.player-container {
  width: 100%;
  max-width: 800px;
  margin: 0 auto;
}

video {
  width: 100%;
  height: auto;
}

.controls {
  margin-top: 10px;
  display: flex;
  gap: 10px;
  justify-content: center;
}
</style>
