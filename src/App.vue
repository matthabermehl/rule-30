<template>
  <div id="app">
    <h3>Wolfram's Rule 30 in Javascript with Vue.js wrapper</h3>
    <h4>
      Author: Matt Habermehl, GitHub:
      <a href="https://github.com/matthabermehl/rule-30" target="_blank"
        >https://github.com/matthabermehl/rule-30</a
      >
    </h4>
    <Canvas msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
import Canvas from "./components/Canvas.vue";

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
