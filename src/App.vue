<template>
  <div id="app">
    <h1 class="mt-16 text-center text-2xl mb-4 font-semibold">TipTap Vue Demo</h1>

    <tip-tap />

  </div>
</template>

<script>
import TipTap from './components/TipTap.vue'


export default {
  name: 'App',
  components: {
    TipTap
  }
}
</script>