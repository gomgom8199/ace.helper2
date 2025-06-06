<template>
  <div>
    <span class="vue text-xs">네이버 아이디 설정</span>
    <div class="py-2"></div>

    <div class="flex py-4">
      <FloatLabel>
        <InputText id="naver_id" size="small" v-model="naverId" @blur="handleBlur" />
        <label class="text-xs " for="naver_id">네이버 아이디</label>
      </FloatLabel>
    </div>
    
    <div class="py-2"></div>

    <div clas="flex py-4">
      <FloatLabel>
        <InputText id="naver_pw" size="small" v-model="naverPw" @blur="handleBlur" />
        <label class="text-xs" for="naver_pw">네이버 패스워드</label>
      </FloatLabel>
    </div>

    <div class="py-4"></div>

    <span class="ace text-xs">에이스옥션 아이디 설정</span>
    <div class="py-2"></div>

    <div class="flex py-4">
      <FloatLabel>
        <InputText id="auction_id" size="small" v-model="auctionId" @blur="handleBlur" />
        <label class="text-xs " for="auction_id">옥션 아이디</label>
      </FloatLabel>
    </div>
    
    <div class="py-2"></div>

    <div clas="flex py-4">
      <FloatLabel>
        <InputText id="auction_pw" size="small" v-model="auctionPw" @blur="handleBlur" />
        <label class="text-xs" for="auction_pw">옥션 패스워드</label>
      </FloatLabel>
    </div>
  </div>
</template>

<script setup>
  import { ref, onMounted } from "vue";
  
  const naverId = ref();
  const naverPw = ref();
  const auctionId = ref();
  const auctionPw = ref();

  onMounted(async () => {
    await getData();
  })

  const getData = async () => {
    const data = await window.electron.ipcRenderer.invoke('checkSettings');
    console.log('getData() -> data', data);

    naverId.value = data.naver_id;
    naverPw.value = data.naver_pw;
    auctionId.value = data.auction_id;
    auctionPw.value = data.auction_pw;
  }

  const handleBlur = (event) => {
    const key = event.target.id;

    if (key === 'naver_id') {
      const settings = {
        key: key,
        value: naverId.value.trim() || null,
      }
      window.electron.ipcRenderer.send('updateSettings', settings);

    } else if (key === 'naver_pw') {
      const settings = {
        key: key,
        value: naverPw.value.trim() || null,
      }
      window.electron.ipcRenderer.send('updateSettings', settings);

    } else if (key === 'auction_id') {
      const settings = {
        key: key,
        value: auctionId.value.trim() || null,
      }
      window.electron.ipcRenderer.send('updateSettings', settings);

    } else if (key === 'auction_pw') {
      const settings = {
        key: key,
        value: auctionPw.value.trim() || null,
      }
      window.electron.ipcRenderer.send('updateSettings', settings);
    }
  };

</script>