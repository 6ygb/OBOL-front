<template>
  <div class="app">
    <OBOL />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import OBOL from './components/OBOL.vue'

export default defineComponent({
  name: 'App',
  components: {
    OBOL
  }
})
</script>

<style>
/* Global styles */
</style>