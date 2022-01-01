<template>
  <div>
    <canvas
      class="canvas"
      ref="canvas"
      :width="width"
      :height="height"
    ></canvas>
    <button @click="clear">Stop</button>
  </div>
</template>

<script>
export default {
  name: "Canvas",
  data() {
    return {
      width: 640,
      height: 480,
      canvas: null,
      ctx: null,
      iteration: null,
      interval: null,
    };
  },
  mounted: function () {
    this.iteration = 0;
    this.canvas = this.$refs.canvas;
    this.ctx = this.canvas.getContext("2d");
    this.setup();
    this.interval = setInterval(() => {
      if (this.iteration == this.height) {
        clearInterval(this.interval);
        return;
      }
      requestAnimationFrame(() => {
        this.evaluate();
      });
      this.iteration++;
    }, 1000 / 24);
  },
  methods: {
    setup() {
      this.ctx.fillStyle = "black";
      this.ctx.fillRect(0, 0, this.width, this.height);
      this.ctx.fillStyle = "white";
      this.ctx.fillRect(Math.floor(this.width / 2), 0, 1, 1);
    },
    clear() {
      clearInterval(this.interval);
    },
    getCanvasData() {
      return this.ctx.getImageData(0, this.iteration, this.width, -1).data;
    },
    evaluate() {
      const fromData = this.getCanvasData();
      const toData = this.ctx.createImageData(this.width, 1);
      for (let i = 0; i < this.width * 4; i += 4) {
        const central = fromData[i] == 255;
        const left = fromData[i - 4] == 255;
        const right = fromData[i + 4] == 255;
        const centralOrRight = central || right;
        if (left ^ centralOrRight) {
          toData.data[i] = 255;
          toData.data[i + 1] = 255;
          toData.data[i + 2] = 255;
          toData.data[i + 3] = 255;
        } else {
          toData.data[i] = 0;
          toData.data[i + 1] = 0;
          toData.data[i + 2] = 0;
          toData.data[i + 3] = 255;
        }
      }
      this.ctx.putImageData(toData, 0, this.iteration);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.canvas {
  width: 1620px;
  height: 1080px;
  display: block;
  margin: 0 auto;
  border: 1px solid black;
}
</style>
