<template>
  <div>
    <h1 class="mb-5">404 Error.</h1>
    <p class="h4">Ahhhhhhhh! The page you are looking for does not exist.</p>
    <p>
      Don't worry, you can head back to
      <router-link to="/"> <span>the main page</span> </router-link>.
    </p>
  </div>
</template>

<script lang="ts">
import { defineComponent } from '@vue/composition-api';

export default defineComponent({
  name: 'Error404',
});
</script>

<style scoped></style>
