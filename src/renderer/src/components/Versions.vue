<template>
  <ul class="versions">
    <li class="vue">Build v{{ appVersion }}</li>
  </ul>
</template>

<script setup>
  import { ref, reactive, onMounted } from 'vue'

  const versions = reactive({ ...window.electron.process.versions }) // const versions = ref({ ...window.electron.process.versions }
  const appVersion = ref();

  onMounted(async () => {
    appVersion.value = await window.electron.ipcRenderer.invoke('getAppVersion'); // package.json의 애플리케이션 버전
  })

  // 지연시간 추가 setTimeOut 대체
  const waitForTimeout = (ms) => {
    return new Promise(resolve => setTimeout(resolve, ms));
  }

</script>