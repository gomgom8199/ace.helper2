<template>
  <div class="card flex justify-center">
    <Menu class="text-xs" :model="items" />
  </div>

  <div class="flex-grow flex justify-center items-center">
    <div v-if="menu === 0">
      <SettingsAuth />
    </div>
    <div v-else-if="menu === 1">
      <SettingsFolder />
    </div>
    <div v-else-if="menu === 2">
      <SettingsOpenAI />
    </div>
    <div v-else-if="menu === 3">
      <SettingsPost />
    </div>
    <div v-else-if="menu === 4">
      <MenusRun />
    </div>
    <div v-else-if="menu === 5">
      <TroubleShooting />
    </div>
  </div>

  <Toast />

</template>
  
<script setup>
  import { ref, onMounted, onUnmounted, watch } from "vue";
  import { useToast } from 'primevue/usetoast';
  import SettingsAuth from "./SettingsAuth.vue";
  import SettingsFolder from "./SettingsFolder.vue";
  import SettingsOpenAI from "./SettingsOpenAI.vue";
  import SettingsPost from "./SettingsPost.vue";
  import MenusRun from "./MenusRun.vue";
  import TroubleShooting from "./TroubleShooting.vue";

  const toast = useToast();

  // emit 정의
  const emit = defineEmits(['close']);

  const menu = ref(0);
  const items = ref([
    {
      label: 'Settings',
      items: [
        {
          label: '아이디 설정',
          icon: 'pi pi-user',
          command: () => {
            menu.value = 0;
          }
        },
        {
          label: '이미지 폴더',
          icon: 'pi pi-folder-open',
          command: () => {
            menu.value = 1;
          }
        },
        {
          label: 'ChatGPT 설정',
          icon: 'pi pi-slack',
          command: () => {
            menu.value = 2;
          }
        },
        {
          label: '포스팅 설정',
          icon: 'pi pi-cog',
          command: () => {
            menu.value = 3;
          }
        },
      ]
    },
    {
      label: 'Menus',
      items: [
        {
          label: '실행',
          icon: 'pi pi-bolt',
          command: () => {
            menu.value = 4;
          }
        },
        {
          label: '문제 해결',
          icon: 'pi pi-wrench',
          command: () => {
            menu.value = 5;
          }
        },
        {
          label: '나가기',
          icon: 'pi pi-sign-out',
          command: () => {
            emit('close');
          }
        }
      ]
    },
  ]);

  const ipcRenderer = window.electron.ipcRenderer;

  const handleUpdateResult = (event, res) => {
    const message = `${res} 수정했습니다.`;
    toast.add({ severity: 'info', summary: '수정됨', detail: message, life: 3000 });
  };

  // 리스너 등록 해제 확실히
  const addUpdateListener = () => {
    ipcRenderer.removeAllListeners('updateResult');
    ipcRenderer.on('updateResult', handleUpdateResult);
  };

  const removeUpdateListener = () => {
    ipcRenderer.removeAllListeners('updateResult');
  };

  // 메뉴 watch로 필요할 때만 리스너 붙임
  watch(menu, (newVal) => {
    const shouldListen = [0, 1, 2, 3].includes(newVal);
    if (shouldListen) {
      addUpdateListener();
    } else {
      removeUpdateListener();
    }
  });

  onMounted(() => {
    if ([0, 1, 2, 3].includes(menu.value)) {
      addUpdateListener();
    }
  });

  onUnmounted(() => {
    removeUpdateListener();
  });

</script>

<style scoped>
</style>