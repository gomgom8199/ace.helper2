<template>
  <img alt="logo" class="logo" src="./assets/electron.svg" />
  <div class="creator">Powered by electron-vite</div>
  <div class="text">
    Ace Auction
    <span class="vue">Helper</span>
  </div>
  <p class="tip">Please try pressing <code>F12</code> to open the devTool</p>
  <div class="actions">

    <!-- ✅ 빌드 환경 -->
    <Button v-if="enableAuction" icon="pi pi-bolt" severity="info" label="프로그램 시작" size="small" @click="overlayAuction" />
    <Button v-else icon="pi pi-spin pi-spinner" severity="info" label="프로그램 시작" size="small" @click="" disabled />

    <!-- ✅ 개발 환경 -->
    <!-- <Button icon="pi pi-bolt" severity="info" label="프로그램 시작" size="small" @click="overlayAuction" /> -->
  </div>

  <div class="pt-4 text-xs">{{ updateProgress }}</div>

  <transition name="slide-left">
    <div v-if="showAuction" class="overlay">
      <Auction @close="closeAuction" />
    </div>
  </transition>

  <Versions />
</template>

<script setup>
  import { ref, onMounted  } from 'vue'
  import Versions from './components/Versions.vue'
  import Auction from './components/Auction.vue'

  const enableAuction = ref(false);
  const showAuction = ref(false);
  const updateProgress = ref();

  onMounted(async () => {
    await waitForTimeout(1000);

    // 1. 업데이트 확인
    await checkUpdate();
  })

  const checkUpdate = async () => {
    window.electron.ipcRenderer.send('checkUpdate');
  }

  /** 업데이트 관련 */
  window.electron.ipcRenderer.on('updateChecking', async (event, res) => {
    console.log(res);
    updateProgress.value = res.message;

    if (res.result === 2) {
      enableAuction.value = true;
    }
  });

  const overlayAuction = async () => {
    showAuction.value = true;
  }

  const closeAuction = () => {
    showAuction.value = false;
  }

  const ipcHandle = () => window.electron.ipcRenderer.send('ping')


  // 지연시간 추가 setTimeOut 대체
  const waitForTimeout = (ms) => {
    return new Promise(resolve => setTimeout(resolve, ms));
  }

</script>

<style scoped>
  .overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 80%;
    height: 100%;
    background-color: rgba(255, 255, 255, 1.0);
    z-index: 1;
    display: flex;
    /* justify-content: center;
    align-items: center; */
    border-radius: 10px;
  }

  .slide-left-enter-active, .slide-left-leave-active {
    transition: all 0.5s ease;
  }

  .slide-left-enter, .slide-left-leave-to {
    transform: translateX(-100%);
    opacity: 0;
  }

  .slide-left-leave {
    transform: translateX(0); /* 기본 위치로 돌아감 */
    opacity: 1;
  }

</style>