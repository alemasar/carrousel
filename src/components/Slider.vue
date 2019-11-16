<template>
  <div class="slider-container">
    {{styles[0]}}
    <ul ref="slider" class="slider">
      <li
        v-bind:style="styles[0]"
        v-bind:class="{ movingUp: isMovingUp[0], movingDown: isMovingDown[0]}"
      >
        <img
          v-bind:style="stylesImg[0]"
          v-bind:class="{ animationImg: isMovingOpacity[0] }"
          src="../assets/01_img.png"
        />
      </li>
      <li
        v-bind:style="styles[1]"
        v-bind:class="{ movingUp: isMovingUp[1], movingDown: isMovingDown[1] }"
      >
        <img
          v-bind:style="stylesImg[1]"
          v-bind:class="{ animationImg: isMovingOpacity[1] }"
          src="../assets/02_img.jpg"
        />
      </li>
      <li
        v-bind:style="styles[2]"
        v-bind:class="{ movingUp: isMovingUp[2], movingDown: isMovingDown[2] }"
      >
        <img
          v-bind:style="stylesImg[2]"
          v-bind:class="{ animationImg: isMovingOpacity[2] }"
          src="../assets/03_img.jpg"
        />
      </li>
      <li
        v-bind:style="styles[3]"
        v-bind:class="{ movingUp: isMovingUp[3], movingDown: isMovingDown[3] }"
      >
        <img
          v-bind:style="stylesImg[3]"
          v-bind:class="{ animationImg: isMovingOpacity[3] }"
          src="../assets/04_img.png"
        />
      </li>
      <li
        v-bind:style="styles[4]"
        v-bind:class="{ movingUp: isMovingUp[4], movingDown: isMovingDown[4] }"
      >
        <img
          v-bind:style="stylesImg[4]"
          v-bind:class="{ animationImg: isMovingOpacity[4] }"
          src="../assets/05_img.jpg"
        />
      </li>
    </ul>
    <ul class="slider-navigator">
      <li v-on:click="changeImage(0)">
        <div class="round"></div>
      </li>
      <li v-on:click="changeImage(1)">
        <div class="round"></div>
      </li>
      <li v-on:click="changeImage(2)">
        <div class="round"></div>
      </li>
      <li v-on:click="changeImage(3)">
        <div class="round"></div>
      </li>
      <li v-on:click="changeImage(4)">
        <div class="round"></div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: "slider",
  data() {
    return {
      initConfig: [],
      styles: [],
      stylesImg: [],
      pos: [0, 1, 2, 1, 0],
      opacity: [0.5, 0.5, 1, 0.5, 0.5],
      top: [2, 1, 0, 1, 2],
      imageSelected: 0,
      x_offset: 100,
      y_offset: 30,
      show: false,
      isMovingDown: [false, false, false, false, false],
      isMovingUp: [false, false, false, false, false],
      isMovingOpacity: [false, false, false, false, false]
    };
  },
  mounted() {
    const imagesLi = this.$refs.slider.querySelectorAll("li");
    imagesLi.forEach((img, i) => {
      const left = (img.offsetWidth - this.x_offset) * i;
      const top = this.y_offset * this.top[i];
      this.initConfig[i] = this.getStyle(left, top, this.pos[i]);
      this.styles.push(this.getStyle(left, top, this.pos[i]));
      this.stylesImg.push({ opacity: this.opacity[i] });
    });

    this.show = true;
  },
  methods: {
    getStyle(left, top, zIndex) {
      return {
        transform: `translate(${left}px, ${top}px)`,
        "z-index": zIndex,
        "background-color": "#fff"
      };
    },
    animationConfig(element, styleObjElement, opacity, delay) {
      this.styles.splice(element, 1, styleObjElement);
      this.initConfig.splice(element, 1, styleObjElement);
      // eslint-disable-next-line no-console
      console.log(element + "  " + opacity);
      this.stylesImg.splice(element, 1, { opacity: opacity });
      this.opacity.splice(element, 1, opacity);
      this.isMovingOpacity.splice(element, 1, true);

      if (delay === "up") {
        this.isMovingUp.splice(element, 1, true);
      } else {
        this.isMovingDown.splice(element, 1, true);
      }
    },
    animationMovingUp(dir) {
      let config = this.initConfig.slice();
      let opacity = this.opacity.slice();
      config.forEach((element, i) => {
        let animation = {};
        if (i === config.length - 1) {
          let pos = config.length - 2
          if (dir===1){
            pos=0;
          }
          animation = () => {
            this.animationConfig(i, config[pos], opacity[pos], "down");
          };
        } else if(i === 0){
          let pos = config.length - 1
          if (dir===1){
            pos=1;
          }
          animation = () => {
            this.animationConfig(i, config[pos], opacity[pos], "down");
          };
        }else {
          animation = () => {
            this.animationConfig(i, config[i + (1*dir)], opacity[i + (1*dir)], "down");
          };
        }
        animation();
        setTimeout(animation, 100 * i);
      });
    },

    changeImage: function(index) {
      let dir = 1;
      this.isMovingDown.splice(0, 5, false, false, false, false, false);
      this.isMovingUp.splice(0, 5, false, false, false, false, false);
      this.isMovingOpacity.splice(0, 5, false, false, false, false, false);
      /*if (index===1){
        this.animationMovingUp(index);
      }*/
      if (index < this.imageSelected){
        dir = -1;
      }
      this.imageSelected = index;
      if (index < this.initConfig.length - 1) {
        this.animationMovingUp(dir);
      } else if (index === this.initConfig.length - 1) {
        this.animationMovingUp(dir);
      } /* else if (index === this.initConfig.length) {
      }*/
    }
  },
  watch: {
    styles: {
      deep: true,
      handler: function(value) {
        // eslint-disable-next-line no-console
        console.log("CAMBIO DE STILO ", value);
      }
    },
    stylesImg: {
      deep: true,
      handler: function(value) {
        // eslint-disable-next-line no-console
        console.log("CAMBIO DE OPACITAT", value);
      }
    },
    isMovingDown: {
      deep: true,
      handler: function(value) {
        // eslint-disable-next-line no-console
        console.log("CAMBIO DE isMovingDown ", value);
      }
    },
    isMovingUp: {
      deep: true,
      handler: function(value) {
        // eslint-disable-next-line no-console
        console.log("CAMBIO DE isMovingUp ", value);
      }
    },
    isMovingOpacity: {
      handler: function(value) {
        // eslint-disable-next-line no-console
        console.log("CAMBIO DE isMovingOpacity ", value);
      }
    }
  }
};
</script>

<style lang="scss" scoped>
.slider-container {
  ul.slider {
    display: flex;
    list-style: none;
    position: relative;
    height: 350px;
    overflow: hidden;

    li {
      width: 250px;
      height: 350px;
      position: absolute;
      img {
        width: 100%;
        height: 100%;
      }
    }
  }
  ul.slider-navigator {
    display: flex;
    list-style: none;
    li {
      background-color: grey;
      width: 25px;
      height: 25px;
      border-radius: 50%;
      margin-right: 20px;
      cursor: pointer;
    }
  }

  .movingUp {
    transition: 0.25s linear;
  }
  .movingDown {
    transition: 0.5s linear;
  }
  .animationImg {
    transition: 0.5s linear;
  }
}
</style>