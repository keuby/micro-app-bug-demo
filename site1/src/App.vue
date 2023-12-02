<template>
  <div class="container">
    <n-button @click="load()">加载基座</n-button>
    <div ref="host" class="iframe-host"></div>
  </div>
</template>

<script setup lang="ts">
import { NButton } from 'naive-ui';
import { ref } from 'vue';
const host = ref<HTMLDivElement>();

const load = () => {
  const iframe = document.createElement('iframe');
  iframe.src = 'http://localhost:3001/';
  iframe.style.width = '100%';
  iframe.style.height = '100%';
  iframe.style.border = 'none';
  host.value?.appendChild(iframe);
};
</script>

<style scoped>
.container {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
}

.iframe-host {
  flex: 1;
  height: 0;
}
</style>
