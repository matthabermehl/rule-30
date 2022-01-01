<template>
  <div id="app">
    <Canvas msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
import Canvas from "./components/Canvas2.vue";

export default {
  name: "App",
  components: {
    Canvas,
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
