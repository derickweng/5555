<template>
  <!-- @mousemove="mousemove($event)" -->
  <div>
    <canvas id="canvas" ref="box" @mouseenter="mouseenter" @mouseleave="mouseleave"></canvas>
    <div class="mouse" v-show="isshow">鼠标放上来喔</div>
  </div>
</template>

<script >
import { _ } from "vue-underscore";
export default {
  data() {
    return {
      time: 1,
      isshow: true
    };
  },
  methods: {
    mouseenter() {
      this.isshow = false;
    },
    mouseleave() {
      this.isshow = true;
    }
  },
  mounted() {
    // 1. 获取当前的画布
    const canvas = document.getElementById("canvas");
    const ctx = canvas.getContext("2d");
    canvas.width = 1000;
    canvas.height = 600;
    canvas.style.backgroundColor = "#000";

    // 2.小球类
    class Ball {
      /**
       * 构造器
       */
      constructor(x, y, color) {
        this.x = x;
        this.y = y;
        this.color = color;
        this.r = 40;
      }

      /**
       * 绘制小球
       */
      render() {
        ctx.save();
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.r, 0, Math.PI * 2);
        ctx.fillStyle = this.color;
        ctx.fill();
        ctx.restore();
      }
    }

    // 3.会移动的小球类
    class MoveBall extends Ball {
      constructor(x, y, color) {
        super(x, y, color);

        // 量的变化
        this.dX = _.random(-5, 5);
        this.dY = _.random(-5, 5);
        this.dR = _.random(1, 3);
      }

      upDate() {
        this.x += this.dX;
        this.y += this.dY;
        this.r -= this.dR;
        if (this.r < 0) {
          this.r = 0;
        }
      }
    }

    // 4. 实例化小球
    let ballArr = [];
    let colorArr = [
      "red",
      "green",
      "blue",
      "yellow",
      "purple",
      "pink",
      "orange"
    ];

    // 5. 监听鼠标的移动
    canvas.addEventListener("mousemove", function(e) {
      ballArr.push(
        new MoveBall(
          e.offsetX,
          e.offsetY,
          colorArr[_.random(0, colorArr.length - 1)]
        )
      );
      // console.log(ballArr);
    });

    // 6.开启定时器
    this.time = setInterval(function() {
      // 清屏
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      console.log(1);
      // 绘制
      for (let i = 0; i < ballArr.length; i++) {
        ballArr[i].render();
        ballArr[i].upDate();
      }
    }, 50);
  },
  beforeDestroy() {
    clearInterval(this.time);
  }
};
</script>

<style>
#canvas {
  width: 100%;
}
.mouse {
  color: #fff;
  position: fixed;
  left: 50%;
  top: 50%;
}
</style>
