<template>
  <div class="d-flex h-100 p-0 align-items-start">
    <textarea v-model="formattedLogs" class="h-100" readonly></textarea>
    <b-button size="sm" @click="botStore.activeBot.getLogs">&#x21bb;</b-button>
  </div>
</template>

<script lang="ts">
import { useBotStore } from '@/stores/ftbotwrapper';
import { defineComponent, onMounted, computed } from '@vue/composition-api';

export default defineComponent({
  name: 'LogViewer',
  setup() {
    const botStore = useBotStore();

    onMounted(() => botStore.activeBot.getLogs());

    const formattedLogs = computed(() => {
      let result = '';
      for (let i = 0, len = botStore.activeBot.lastLogs.length; i < len; i += 1) {
        const log = botStore.activeBot.lastLogs[i];
        result += `${log[0]} - ${log[2]} - ${log[3]} - ${log[4]}\n`;
      }
      return result;
    });

    return {
      botStore,
      formattedLogs,
    };
  },
});
</script>

<style lang="scss" scoped>
textarea {
  width: 100%;
  min-height: 6em;
  resize: none;
  font-size: $fontsize-small;
}
</style>
