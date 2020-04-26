<template>
  <div id="app">
    <cssGradientGenerate></cssGradientGenerate>
  </div>
</template>

<script>
import cssGradientGenerate from './components/cssGradientGenerate.vue'

export default {
  name: 'App',
  components: {
    cssGradientGenerate
  }
}
</script>

<style>
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
</style>
