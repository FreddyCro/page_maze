<template>
  <div
    :class="{
      'page-container': true,
      'page--disabled': !ON_THIS_PAGE
    }"
    :style="{
      left: pagePosition[0],
      top: pagePosition[1],
      backgroundColor: colorScheme[pageInfo.category - 1] + 99,
    }"
  >
    <div class="page-image">
      <div
        :class="{
          'page-image-complete': true,
          'page-image-complete--active': imageCompleted,
        }"
      >
        <img :width="pageImageSize + 'px'" :src="pageInfo.cover" :alt="pageInfo.title">
      </div>
      <canvas
        :id="'page-image' + id"
        :class="{
          'page-image-canvas': true,
          'page-image-canvas--disabled': imageCompleted,
        }"
      />
    </div>
    <div class="page-body">
      <div class="page-title">
        <h1>{{pageInfo.title}}</h1>
      </div>
      <div class="page-description">
        <p>{{pageInfo.description}}</p>
      </div>
    </div>
  </div>
</template>

<script>
import { TimelineMax } from "gsap/TweenMax";

export default {
  name: "Page",
  props: {
    pageInfo: {
      type: Object,
      required: true
    },
    id: {
      type: String,
      required: true
    },
    currentCoords: {
      type: Object,
      required: true
    },
    colorScheme: {
      type: Array,
    }
  },
  data() {
    return {
      imageCompleted: false,
    };
  },
  computed: {
    isMob() {
      return window.innerWidth < 769 ? true : false;
    },
    pageImageSize() {
      return this.isMob ? 200 : 360
    },
    ON_THIS_PAGE() {
      return this.currentCoords.x === this.pageInfo.x && this.currentCoords.y === this.pageInfo.y;
    },
    pagePosition() {
      return [this.pageInfo.x * 100 + "%", this.pageInfo.y * 100 + "vh"];
    },
  },
  methods: {
    drawParticules() {
      const vm = this;
      const canvas = document.getElementById("page-image" + vm.id);
      const context = canvas.getContext("2d");
      const img = new Image();
      const particules_max = 1500;
      const paritcule_size = 8;
      let particlesDistance = 0;
      let pixelCoordinates, pixels, raf;

      init();
      function init() {
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
        drawImage();
      }
      function drawImage() {
        img.crossOrigin = "Anonymous";
        img.src = vm.pageInfo.cover;

        img.onload = function() {
          context.drawImage(
            img,
            0,
            0,
            img.width,
            img.height,
            (img.width - vm.pageImageSize) / 2,
            (img.height- vm.pageImageSize) / 2,
            vm.pageImageSize,
            vm.pageImageSize
          );
          getImageData();
        };
      }
      function getImageData() {
        const imageData = context.getImageData(0, 0, img.width, img.height)
          .data;
        pixelCoordinates = [];
        pixels = [];

        for (let i = 0; i < imageData.length; i = i + 4) {
          if (imageData[i] !== 0) {
            const currentPixel = i / 4;
            const x = currentPixel % img.width;
            const y = ~~(currentPixel / img.width);

            pixelCoordinates.push({
              x: x,
              y: y,
              red: imageData[i],
              green: imageData[i + 1],
              blue: imageData[i + 2],
              alpha: imageData[i + 3]
            });
          }
        }
        generateParticuleImage();
        function generateParticuleImage() {
          let particuleOffset = 0;

          if (pixelCoordinates.length > particules_max) {
            particuleOffset = ~~(pixelCoordinates.length / particules_max);
          }

          for (let i = 0; i < particules_max; i++) {
            const pIndex = particuleOffset * i;
            const pixelOptions = {
              x: pixelCoordinates[pIndex].x,
              y: pixelCoordinates[pIndex].y,
              colors: {
                red: pixelCoordinates[pIndex].red,
                green: pixelCoordinates[pIndex].green,
                blue: pixelCoordinates[pIndex].blue,
                alpha: pixelCoordinates[pIndex].alpha
              }
            };
            const pixel = new particule(pixelOptions);

            pixels.push(pixel);
          }

          context.clearRect(0, 0, canvas.width, canvas.height);
          render();
          generateTimelines();
        }
      }
      function onMouseMove(ev) {
        const mousePos = ev * 2;
        for (let i = 0; i < pixels.length; i++) {
          pixels[i].timeline.time(mousePos);
        }
      }
      function render() {
        context.clearRect(0, 0, canvas.width, canvas.height);
        for (let i = 0; i < pixels.length; i++) {
          pixels[i].draw();
        }

        if (vm.ON_THIS_PAGE) {
          onMouseMove(Math.min(particlesDistance += 0.03, 1));
          vm.imageCompleted = particlesDistance >= 0.7 ? true : false ;
        }
        else {
          vm.imageCompleted = false;
          particlesDistance = 0;
        }

        raf = window.requestAnimationFrame(render);
      }
      function generateTimelines() {
        for (let i = 0; i < pixels.length; i++) {
          pixels[i].animate();
        }
      }
      function destroyParticules() {
        for (let i = 0; i < pixels.length; i++) {
          pixels[i] = null;
          delete pixels[i];
        }
      }
      class particule {
        constructor(options) {
          const centerOffsetX = canvas.width / 2 - img.width / 2;
          const centerOffsetY = canvas.height / 2 - img.height / 2;
          this.centerPosX = centerOffsetX + options.x;
          this.centerPosY = centerOffsetY + options.y;
          this.posX = getRandomInt(0, canvas.width);
          this.posY = getRandomInt(0, canvas.height);
          this.timeline = new TimelineMax({
            paused: true
          });
          this.colors = options.colors;
        }
        draw() {
          context.beginPath();

          context.arc(this.posX, this.posY, paritcule_size, 0, 2 * Math.PI);

          context.fillStyle = `rgb( ${this.colors.red},${this.colors.green},${this.colors.blue})`;
          context.globalAlpha = this.colors.alpha;
          context.fill();
        }
        animate() {
          this.timeline.to(this, 1, {
            posX: this.centerPosX,
            posY: this.centerPosY,
            delay: getRandomFloat(0, 0.001)
          });
        }
      }
      function getRandomInt(min, max) {
        return ~~(Math.random() * (max - min + 1)) + min;
      }

      function getRandomFloat(min, max) {
        return Math.random() * (max - min + 1) + min;
      }
    },
  },
  mounted() {
    this.drawParticules();
  }
};
</script>

<style lang="scss" scoped>
.page-container {
  position: absolute;
  height: 100vh;
  width: 100%;
  color: #ffffff;
  text-align: center;
  .page-image {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    .page-image-complete {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      opacity: 0;
      transition: opacity 1s ease-in-out;
    }
    .page-image-complete--active {
      opacity: 1;
      transform: scale(1.1);
      transform-origin: 50% 50%;
    }
    .page-image-canvas {
      transition: opacity 1s ease-in-out;    
    }    
    .page-image-canvas--disabled {
      opacity: 0;
    }
  }
  .page-body {
    position: relative;;
    width: 100%;
    height: 100%;
    padding: 10%;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    @media only screen and (min-width: 769px){
      justify-content: space-between;
    }
    .page-title {
      h1 {
        font-size: 42px;
      }
      margin-bottom: 20px;
    }
    .page-description {
      margin-top: 50px;      
    }
  }
}
.page--disabled {
  animation: page-disabled-animation .666s ease-in-out forwards;
  @keyframes page-disabled-animation {
    from {
      opacity: 1;
    }
    to {
      opacity: 0;
    }
  }
}
</style>