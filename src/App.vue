<template>
  <div id="app">
<!--    <cssGradientGenerate></cssGradientGenerate>-->
    <cssGradientGenerateNew></cssGradientGenerateNew>
  </div>
</template>

<script>
import cssGradientGenerate from './components/cssGradientGenerate.vue'
import cssGradientGenerateNew from './components/cssGradientGenerateNew'

export default {
  name: 'App',
  components: {
    cssGradientGenerate,
    cssGradientGenerateNew
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
