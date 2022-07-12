<template>
  <div id="app">
    <webgl/>
  </div>
</template>

<script>
import webgl from './components/webgl.vue'

export default {
  name: 'App',
  components: {
    webgl
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  padding:0;
  margin: 0;
}
</style>
