<template>
  <div class="container">
    <n-button @click="load()">初始化沙箱</n-button>
    <div ref="host" class="iframe-host"></div>
  </div>
</template>

<script setup lang="ts">
import { NButton } from 'naive-ui';
import { ref } from 'vue';

const host = ref<HTMLDivElement>();

const load = async () => {
  const iframe = createIframe(location.origin);
  await awaitSandboxReady(iframe);
  console.log('sandboxReady');
};

const createIframe = (target: string) => {
  const iframe = document.createElement('iframe');
  iframe.src = target;
  iframe.style.border = 'none';
  iframe.style.display = 'none';
  // iframe.style.width = '100%';
  // iframe.style.height = '100%';
  host.value?.appendChild(iframe);
  return iframe;
};

const awaitSandboxReady = (iframe: HTMLIFrameElement) => {
  let oldMicroDocument: Document;
  let microAppWindow: Window;
  try {
    microAppWindow = iframe!.contentWindow!;
    oldMicroDocument = microAppWindow.document;
  } catch {
    return Promise.resolve();
  }

  return new Promise<void>((resolve) => {
    (function iframeLocationReady() {
      setTimeout(() => {
        try {
          /**
           * NOTE:
           *  1. In browser, iframe document will be recreated after iframe initial
           *  2. In jest, iframe document is always the same
           */
          if (microAppWindow.document === oldMicroDocument) {
            iframeLocationReady();
          } else {
            /**
             * NOTE:
             *  1. microAppWindow will not be recreated
             *  2. the properties of microAppWindow may be recreated, such as document
             *  3. the variables added to microAppWindow may be cleared
             */
            microAppWindow.stop();
            resolve();
          }
        } catch (e) {
          iframeLocationReady();
        }
      }, 1);
    })();
  });
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
