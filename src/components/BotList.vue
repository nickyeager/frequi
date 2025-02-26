<template>
  <div v-if="botStore.botCount > 0">
    <h3 v-if="!small">Available bots</h3>
    <b-list-group>
      <b-list-group-item
        v-for="bot in botStore.availableBots"
        :key="bot.botId"
        :active="bot.botId === botStore.selectedBot"
        button
        :title="`${bot.botId} - ${bot.botName} - ${bot.botUrl}`"
        @click="botStore.selectBot(bot.botId)"
      >
        <bot-rename
          v-if="editingBots.includes(bot.botId)"
          :bot="bot"
          @saved="stopEditBot(bot.botId)"
          @cancelled="stopEditBot(bot.botId)"
        />

        <bot-entry v-else :bot="bot" :no-buttons="small" @edit="editBot(bot.botId)" />
      </b-list-group-item>
    </b-list-group>
    <LoginModal v-if="!small" class="mt-2" login-text="Add new bot" />
  </div>
</template>

<script lang="ts">
import LoginModal from '@/views/LoginModal.vue';
import BotEntry from '@/components/BotEntry.vue';
import BotRename from '@/components/BotRename.vue';

import { defineComponent, ref } from '@vue/composition-api';
import { useBotStore } from '@/stores/ftbotwrapper';

export default defineComponent({
  name: 'BotList',
  components: { LoginModal, BotEntry, BotRename },
  props: {
    small: { default: false, type: Boolean },
  },
  setup() {
    const botStore = useBotStore();

    const editingBots = ref<string[]>([]);

    const editBot = (botId: string) => {
      if (!editingBots.value.includes(botId)) {
        editingBots.value.push(botId);
      }
    };

    const stopEditBot = (botId: string) => {
      if (!editingBots.value.includes(botId)) {
        return;
      }

      editingBots.value.splice(editingBots.value.indexOf(botId), 1);
    };

    return {
      botStore,
      editingBots,
      editBot,
      stopEditBot,
    };
  },
});
</script>
