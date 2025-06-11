<template>
  <div>
    <span class="vue text-xs">포스팅 설정</span>

    <div class="py-4"></div>

    <div class="flex py-4">
      <FloatLabel class="w-full md:w-48">
        <Select class="w-full text-xs" v-model="isPublish" inputId="isPublish" :options="isPublishs" optionLabel="name" @change="isPublishsChange" />
        <label class="text-xs" for="isPublish">발행·저장 여부</label>
      </FloatLabel>
    </div>

    <div class="py-2"></div>

    <div class="flex py-4">
      <FloatLabel class="w-full md:w-48">
        <Select class="w-full text-xs" v-model="quote" inputId="quote" :options="quotes" optionLabel="name" @change="quoteChange" />
        <label class="text-xs" for="quote">인용구 타입</label>
      </FloatLabel>
    </div>

    <div class="py-2"></div>

    <div class="flex py-4">
      <FloatLabel class="w-full md:w-48">
        <Select class="w-full text-xs" v-model="font" inputId="font" :options="fonts" optionLabel="name" @change="fontChange" />
        <label class="text-xs" for="font">워터마크 폰트</label>
      </FloatLabel>
    </div>

    <div class="flex py-4">
      <FloatLabel>
        <InputText id="water_text" size="small" v-model="waterText" @blur="handleBlur" />
        <label class="text-xs " for="water_text">워터마크 텍스트</label>
      </FloatLabel>
    </div>

    <div class="flex">
      <div class="flex-1 flex flex-col items-center">
        <TwitterPicker v-model="colorHEX" @update:modelValue="colorChange" />
        <span 
          class="pt-4 flex-1 flex flex-col items-center text-black text-2xl"
          :style="{ fontFamily: font.val, color: colorHEX }"
        >
          {{ waterText || '미리보기' }}
        </span>
      </div>
    </div>

  </div>

</template>

<script setup>
  import { ref, onMounted } from "vue";
  import { TwitterPicker } from 'vue-color'
  import NanumSquareEB from '../../../../resources/fonts/nanum-square/NanumSquareEB.ttf?url'
  import NanumSquareB from '../../../../resources/fonts/nanum-square/NanumSquareB.ttf?url'
  import NanumSquareR from '../../../../resources/fonts/nanum-square/NanumSquareR.ttf?url'
  import NanumSquareL from '../../../../resources/fonts/nanum-square/NanumSquareL.ttf?url'
  import NanumPen from '../../../../resources/fonts/nanum-pen/NanumPen.ttf?url'
  import NanumBrush from '../../../../resources/fonts/nanum-brush/NanumBrush.ttf?url'

  const isPublish = ref();
  const isPublishs = ref([
    { name: '발행', code: true },
    { name: '저장', code: false },
  ])
  const quote = ref();
  const quotes = ref([
    { name: '랜덤', code: 0 },
    { name: '따옴표', code: 1 },
    { name: '버티컬 라인', code: 2 },
    { name: '말풍선', code: 3 },
    { name: '라인&따옴표', code: 4 },
    { name: '포스트잇', code: 5 },
    { name: '프레임', code: 6 },
  ])
  const font = ref({ name: '나눔스퀘어 (Regular)', val: 'NanumSquareR' });
  const fonts = ref([
    { name: '나눔스퀘어 (Extra Bold)', val: 'NanumSquareEB' },
    { name: '나눔스퀘어 (Bold)', val: 'NanumSquareB' },
    { name: '나눔스퀘어 (Regular)', val: 'NanumSquareR' },
    { name: '나눔스퀘어 (Light)', val: 'NanumSquareL' },
    { name: '나눔손글씨 펜', val: 'NanumPen' },
    { name: '나눔손글씨 붓', val: 'NanumBrush' },
  ])
  const colorHEX = ref('#0693e3');
  const waterText = ref();

  onMounted (async () => {
    await getData();

    const loadFont = (name, url) => {
      const font = new FontFace(name, `url(${url})`)
      font.load().then((loadedFont) => {
        document.fonts.add(loadedFont)
      }).catch(err => {
        console.error('폰트 로딩 실패:', name, err)
      });
    }
    loadFont('NanumSquareEB', NanumSquareEB)
    loadFont('NanumSquareB', NanumSquareB)
    loadFont('NanumSquareR', NanumSquareR)
    loadFont('NanumSquareL', NanumSquareL)
    loadFont('NanumPen', NanumPen)
    loadFont('NanumBrush', NanumBrush)
  })

  const getData = async () => {
    const data = await window.electron.ipcRenderer.invoke('checkSettings');
    console.log('getData() -> data', data);

    isPublish.value =  isPublishs.value.find(item => item.code === data.is_publish);
    quote.value = quotes.value.find(item => item.code === data.quote);
    font.value = fonts.value.find(item => item.val === data.font);
    colorHEX.value = data.font_color;
    waterText.value = data.water_text;
  }

  const isPublishsChange = (event) => {
    const settings = {
      key: 'is_publish',
      value: isPublish.value.code,
    }
    window.electron.ipcRenderer.send('updateSettings', settings);
  }

  const quoteChange = (event) => {
    const settings = {
      key: 'quote',
      value: quote.value.code,
    }
    window.electron.ipcRenderer.send('updateSettings', settings);
  }

  const fontChange = (event) => {
    const settings = {
      key: 'font',
      value: font.value.val,
    }
    window.electron.ipcRenderer.send('updateSettings', settings);
  }

  const colorChange = (event) => {
    const settings = {
      key: 'font_color',
      value: colorHEX.value,
    }
    window.electron.ipcRenderer.send('updateSettings', settings);
  }

  const handleBlur = (event) => {
    const key = event.target.id;

    if (key === 'water_text') {
      const settings = {
        key: key,
        value: waterText.value.trim() || null,
      }
      window.electron.ipcRenderer.send('updateSettings', settings);
    }
  };

</script>