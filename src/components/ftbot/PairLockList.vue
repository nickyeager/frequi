<template>
  <div>
    <div class="mb-2">
      <label class="mr-auto h3">Pair Locks</label>
      <b-button class="float-right" size="sm" @click="botStore.activeBot.getLocks"
        >&#x21bb;</b-button
      >
    </div>
    <div>
      <b-table class="table-sm" :items="botStore.activeBot.activeLocks" :fields="tableFields">
        <template #cell(actions)="row">
          <b-button
            class="btn-xs ml-1"
            size="sm"
            title="Delete trade"
            @click="removePairLock(row.item)"
          >
            <DeleteIcon :size="16" />
          </b-button>
        </template>
      </b-table>
    </div>
  </div>
</template>

<script lang="ts">
import { timestampms } from '@/shared/formatters';
import { Lock } from '@/types';

import DeleteIcon from 'vue-material-design-icons/Delete.vue';
import { showAlert } from '@/stores/alerts';
import { defineComponent } from '@vue/composition-api';
import { useBotStore } from '@/stores/ftbotwrapper';

export default defineComponent({
  name: 'PairLockList',
  components: { DeleteIcon },
  setup() {
    const botStore = useBotStore();

    const tableFields = [
      { key: 'pair', label: 'Pair' },
      { key: 'lock_end_timestamp', label: 'Until', formatter: 'timestampms' },
      { key: 'reason', label: 'Reason' },
      { key: 'actions' },
    ];

    const removePairLock = (item: Lock) => {
      console.log(item);
      if (item.id !== undefined) {
        botStore.activeBot.deleteLock(item.id);
      } else {
        showAlert('This Freqtrade version does not support deleting locks.');
      }
    };

    return {
      timestampms,
      botStore,
      tableFields,
      removePairLock,
    };
  },
});
</script>

<style scoped></style>
