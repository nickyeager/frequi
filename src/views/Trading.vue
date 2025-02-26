<template>
  <GridLayout
    class="h-100 w-100"
    :row-height="50"
    :layout="gridLayout"
    :vertical-compact="false"
    :margin="[5, 5]"
    :responsive-layouts="responsiveGridLayouts"
    :is-resizable="!isLayoutLocked"
    :is-draggable="!isLayoutLocked"
    :responsive="true"
    :cols="{ lg: 12, md: 12, sm: 12, xs: 4, xxs: 2 }"
    @layout-updated="layoutUpdatedEvent"
    @breakpoint-changed="breakpointChanged"
  >
    <GridItem
      v-if="gridLayoutMultiPane.h != 0"
      :i="gridLayoutMultiPane.i"
      :x="gridLayoutMultiPane.x"
      :y="gridLayoutMultiPane.y"
      :w="gridLayoutMultiPane.w"
      :h="gridLayoutMultiPane.h"
      drag-allow-from=".card-header"
    >
      <DraggableContainer header="Multi Pane">
        <b-tabs content-class="mt-3" class="mt-1">
          <b-tab title="Pairs combined" active>
            <PairSummary
              :pairlist="botStore.activeBot.whitelist"
              :current-locks="botStore.activeBot.activeLocks"
              :trades="botStore.activeBot.openTrades"
            />
          </b-tab>
          <b-tab title="General">
            <div class="d-flex justify-content-center">
              <BotControls class="mt-1 mb-2" />
            </div>
            <BotStatus />
          </b-tab>
          <b-tab title="Performance">
            <Performance class="performance-view" />
          </b-tab>
          <b-tab title="Balance" lazy>
            <Balance />
          </b-tab>
          <b-tab title="Daily Stats" lazy>
            <DailyStats />
          </b-tab>

          <b-tab title="Pairlist" lazy>
            <FTBotAPIPairList />
          </b-tab>
          <b-tab title="Pair Locks" lazy>
            <PairLockList />
          </b-tab>
        </b-tabs>
      </DraggableContainer>
    </GridItem>
    <GridItem
      v-if="gridLayoutOpenTrades.h != 0"
      :i="gridLayoutOpenTrades.i"
      :x="gridLayoutOpenTrades.x"
      :y="gridLayoutOpenTrades.y"
      :w="gridLayoutOpenTrades.w"
      :h="gridLayoutOpenTrades.h"
      drag-allow-from=".card-header"
    >
      <DraggableContainer header="Open Trades">
        <TradeList
          class="open-trades"
          :trades="botStore.activeBot.openTrades"
          title="Open trades"
          :active-trades="true"
          empty-text="Currently no open trades."
        />
      </DraggableContainer>
    </GridItem>
    <GridItem
      v-if="gridLayoutTradeHistory.h != 0"
      :i="gridLayoutTradeHistory.i"
      :x="gridLayoutTradeHistory.x"
      :y="gridLayoutTradeHistory.y"
      :w="gridLayoutTradeHistory.w"
      :h="gridLayoutTradeHistory.h"
      drag-allow-from=".card-header"
    >
      <DraggableContainer header="Closed Trades">
        <TradeList
          class="trade-history"
          :trades="botStore.activeBot.closedTrades"
          title="Trade history"
          :show-filter="true"
          empty-text="No closed trades so far."
        />
      </DraggableContainer>
    </GridItem>
    <GridItem
      v-if="botStore.activeBot.detailTradeId && gridLayoutTradeDetail.h != 0"
      :i="gridLayoutTradeDetail.i"
      :x="gridLayoutTradeDetail.x"
      :y="gridLayoutTradeDetail.y"
      :w="gridLayoutTradeDetail.w"
      :h="gridLayoutTradeDetail.h"
      :min-h="4"
      drag-allow-from=".card-header"
    >
      <DraggableContainer header="Trade Detail">
        <TradeDetail
          :trade="botStore.activeBot.tradeDetail"
          :stake-currency="botStore.activeBot.stakeCurrency"
        />
      </DraggableContainer>
    </GridItem>
    <GridItem
      v-if="gridLayoutTradeDetail.h != 0"
      :i="gridLayoutChartView.i"
      :x="gridLayoutChartView.x"
      :y="gridLayoutChartView.y"
      :w="gridLayoutChartView.w"
      :h="gridLayoutChartView.h"
      :min-h="6"
      drag-allow-from=".card-header"
    >
      <DraggableContainer header="Chart">
        <CandleChartContainer
          :available-pairs="botStore.activeBot.whitelist"
          :historic-view="!!false"
          :timeframe="botStore.activeBot.timeframe"
          :trades="botStore.activeBot.allTrades"
        >
        </CandleChartContainer>
      </DraggableContainer>
    </GridItem>
  </GridLayout>
</template>

<script lang="ts">
import { GridLayout, GridItem, GridItemData } from 'vue-grid-layout';

import Balance from '@/components/ftbot/Balance.vue';
import BotControls from '@/components/ftbot/BotControls.vue';
import BotStatus from '@/components/ftbot/BotStatus.vue';
import CandleChartContainer from '@/components/charts/CandleChartContainer.vue';
import DailyStats from '@/components/ftbot/DailyStats.vue';
import DraggableContainer from '@/components/layout/DraggableContainer.vue';
import FTBotAPIPairList from '@/components/ftbot/FTBotAPIPairList.vue';
import PairLockList from '@/components/ftbot/PairLockList.vue';
import PairSummary from '@/components/ftbot/PairSummary.vue';
import Performance from '@/components/ftbot/Performance.vue';
import TradeDetail from '@/components/ftbot/TradeDetail.vue';
import TradeList from '@/components/ftbot/TradeList.vue';

import { defineComponent, ref, computed } from '@vue/composition-api';
import { useLayoutStore, findGridLayout, TradeLayout } from '@/stores/layout';
import { useBotStore } from '@/stores/ftbotwrapper';

export default defineComponent({
  name: 'Trading',
  components: {
    Balance,
    BotControls,
    BotStatus,
    CandleChartContainer,
    DailyStats,
    DraggableContainer,
    FTBotAPIPairList,
    GridItem,
    GridLayout,
    PairLockList,
    PairSummary,
    Performance,
    TradeDetail,
    TradeList,
  },
  setup() {
    const botStore = useBotStore();
    const layoutStore = useLayoutStore();
    const currentBreakpoint = ref('');

    const breakpointChanged = (newBreakpoint) => {
      // console.log('breakpoint:', newBreakpoint);
      currentBreakpoint.value = newBreakpoint;
    };
    const isResizableLayout = computed(() =>
      ['', 'sm', 'md', 'lg', 'xl'].includes(currentBreakpoint.value),
    );
    const isLayoutLocked = computed(() => {
      return layoutStore.layoutLocked || !isResizableLayout;
    });
    const gridLayout = computed((): GridItemData[] => {
      if (isResizableLayout) {
        return layoutStore.tradingLayout;
      }
      return [...layoutStore.getTradingLayoutSm];
    });

    const gridLayoutMultiPane = computed(() => {
      return findGridLayout(gridLayout.value, TradeLayout.multiPane);
    });

    const gridLayoutOpenTrades = computed(() => {
      return findGridLayout(gridLayout.value, TradeLayout.openTrades);
    });

    const gridLayoutTradeHistory = computed(() => {
      return findGridLayout(gridLayout.value, TradeLayout.tradeHistory);
    });

    const gridLayoutTradeDetail = computed(() => {
      return findGridLayout(gridLayout.value, TradeLayout.tradeDetail);
    });

    const gridLayoutChartView = computed(() => {
      return findGridLayout(gridLayout.value, TradeLayout.chartView);
    });

    const responsiveGridLayouts = computed(() => {
      return {
        sm: layoutStore.getTradingLayoutSm,
      };
    });

    const layoutUpdatedEvent = (newLayout) => {
      if (isResizableLayout) {
        layoutStore.tradingLayout = newLayout;
      }
    };

    return {
      botStore,

      layoutStore,
      breakpointChanged,
      layoutUpdatedEvent,
      isLayoutLocked,
      gridLayout,
      gridLayoutMultiPane,
      gridLayoutOpenTrades,
      gridLayoutTradeHistory,
      gridLayoutTradeDetail,
      gridLayoutChartView,
      responsiveGridLayouts,
      isResizableLayout,
    };
  },
});
</script>

<style scoped></style>
