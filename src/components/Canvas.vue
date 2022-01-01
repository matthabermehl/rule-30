<template>
  <div>
    <canvas class="canvas" ref="canvas" :width="width" :height="height"></canvas>
    <button @click="clear">Stop</button>
  </div>
</template>

<script>
export default {
  name: "Canvas",
  data() {
    return {
      width: 320,
      height: 200,
      canvas: null,
      ctx: null,
      iteration: null,
      interval: null,
    };
  },
  mounted: function () {
    this.iteration = 1000;
    this.canvas = this.$refs.canvas;
    this.ctx = this.canvas.getContext("2d");
    this.setup();
    this.interval = setInterval(() => {
      if (this.iteration == 0) {
        clearInterval(this.interval);
        return;
      }
      requestAnimationFrame(() => {
        this.evaluate();
      });
      this.iteration--;
    }, 1000 / 24);
  },
  methods: {
    setup() {
      this.ctx.fillStyle = "black";
      this.ctx.fillRect(0, 0, this.width, this.height);
      this.ctx.fillStyle = "white";
      this.ctx.fillRect(2, 2, 1, 1);
    },
    clear() {
      clearInterval(this.interval);
    },
    getPixel(x, y) {
      return this.ctx.getImageData(x, y, 1, 1).data;
    },
    getNeighbours(x, y) {
      const neighbours = [];
      for (let i = -1; i <= 1; i++) {
        for (let j = -1; j <= 1; j++) {
          if (i == 0 && j == 0) {
            continue;
          }
          const neighbour = {
            x: x + i,
            y: y + j,
          };
          neighbours.push(neighbour);
        }
      }
      return neighbours;
    },
    getCanvasData() {
      return this.ctx.getImageData(0, 0, this.width, this.height).data;
    },
    getWhitePixels() {
      const numberOfPixels = this.width * this.height;
      const data = this.getCanvasData();
      const whitePixels = [];
      for (let i = 0; i < numberOfPixels; i++) {
        if (data[i * 4] === 255) {
          whitePixels.push({
            x: i % this.width,
            y: Math.floor(i / this.width),
          });
        }
      }
      return whitePixels;
    },
    changePixelToWhite(x, y) {
      this.ctx.fillStyle = "white";
      this.ctx.fillRect(x, y, 1, 1);
    },
    changePixelToBlack(x, y) {
      this.ctx.fillStyle = "black";
      this.ctx.fillRect(x, y, 1, 1);
    },
    pixelIsBlack(x, y) {
      const pixel = this.getPixel(x, y);
      return pixel[0] == 0 && pixel[1] == 0 && pixel[2] == 0;
    },
    pixelIsWhite(x, y) {
      const pixel = this.getPixel(x, y);
      return pixel[0] == 255 && pixel[1] == 255 && pixel[2] == 255;
    },
    evaluate() {
      this.getWhitePixels().forEach((pixel) => {
        const neighbours = this.getNeighbours(pixel.x, pixel.y);
        const blackNeighbours = neighbours.filter((neighbour) => {
          return this.pixelIsBlack(neighbour.x, neighbour.y);
        });
        const whiteNeighbours = neighbours.filter((neighbour) => {
          return this.pixelIsWhite(neighbour.x, neighbour.y);
        });
        if (whiteNeighbours.length > 1) {
          this.changePixelToBlack(pixel.x, pixel.y);
        }
        blackNeighbours.forEach((neighbour) => {
          const flipACoin = Math.random() < 0.4;
          if (flipACoin) {
            this.changePixelToWhite(neighbour.x, neighbour.y);
          }
        });
      });
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
