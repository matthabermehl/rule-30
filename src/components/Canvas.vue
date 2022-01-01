<template>
  <div>
    <canvas
      class="canvas"
      ref="canvas"
      :width="width"
      :height="height"
      :style="style"
    ></canvas>
    <button @click="clear">Stop</button>
    <button @click="restart">Restart</button>
    <button @click="cycleSize">Cycle Size</button>
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
      sizeCycle: 0,
      size: [
        {
          width: 640,
          height: 480,
        },
        {
          width: 800,
          height: 600,
        },
        {
          width: 1024,
          height: 768,
        },
        {
          width: 1280,
          height: 1024,
        },
      ],
    };
  },
  mounted: function () {
    this.iteration = 0;
    this.canvas = this.$refs.canvas;
    this.ctx = this.canvas.getContext("2d");
    this.cycleSize();
  },
  methods: {
    setup() {
      this.$nextTick(() => {
        this.ctx.fillStyle = "black";
        this.ctx.fillRect(0, 0, this.width, this.height);
        this.ctx.fillStyle = "white";
        this.ctx.fillRect(Math.floor(this.width / 2), 0, 1, 1);
      });
    },
    clear() {
      clearInterval(this.interval);
    },
    restart() {
      this.clear();
      this.setup();
      this.iteration = 0;
      this.interval = setInterval(() => {
        if (this.iteration == this.height) {
          this.clear();
          return;
        }
        requestAnimationFrame(() => {
          this.evaluate();
        });
        this.iteration++;
      }, 1000 / 24);
    },
    cycleSize() {
      this.clear();
      if (this.sizeCycle == this.size.length) {
        this.sizeCycle = 0;
      }
      this.width = this.size[this.sizeCycle].width;
      this.height = this.size[this.sizeCycle].height;
      this.restart();
      this.sizeCycle++;
    },
    getCanvasLine() {
      return this.ctx.getImageData(0, this.iteration, this.width, -1).data;
    },
    evaluate() {
      const fromData = this.getCanvasLine();
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
  computed: {
    style() {
      return {
        width: this.width + "px",
        height: this.height + "px",
      };
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.canvas {
  display: block;
  margin: 0 auto;
  border: 1px solid black;
}
</style>
