<template>
  <div class="home">
    <div class="container col-12 col-sm-6 col-lg-4">
      <bot-list />
    </div>
    <hr />
    <img alt="Freqtrade logo" src="../assets/y-logo.png" width="450px" class="my-5" />
    <!--    <div alt="Freqtrade logo" class="logo-svg my-5 mx-auto" />-->
    <div>
      <h1>Welcome to the Yeager Corp</h1>
    </div>
    <div>Dashboard for backtesting/controlling trading bots.</div>
    <br />
    <p>
      Docs
      <a href="https://www.freqtrade.io/en/latest/">Freqtrade Documentation</a>.
    </p>
  </div>
</template>

<script lang="ts">
import { defineComponent } from '@vue/composition-api';

import BotList from '@/components/BotList.vue';

export default defineComponent({
  name: 'Home',
  components: { BotList },
});
</script>

<style lang="scss" scoped>
.home {
  margin-top: 1.5em;
}
.logo-svg {
  -webkit-mask: url(../assets/y-logo.png) no-repeat center;
  -webkit-mask-size: 240px 240px;
  mask: url(../assets/y-logo.png) no-repeat center;
  mask-size: contain;
  width: 250px;
  height: 250px;
}
</style>
