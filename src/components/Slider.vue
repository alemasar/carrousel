<template>
  <div class="slider-container">
    {{styles[0]}}
    <ul ref="slider" class="slider">
      <li
        v-bind:style="styles[0]"
        v-bind:class="{ movingUp: isMovingUp[0], movingDown: isMovingDown[0]}"
      >
        <img src="../assets/01_img.png" />
      </li>
      <li
        v-bind:style="styles[1]"
        v-bind:class="{ movingUp: isMovingUp[1], movingDown: isMovingDown[1] }"
      >
        <img src="../assets/02_img.jpg" />
      </li>
      <li
        v-bind:style="styles[2]"
        v-bind:class="{ movingUp: isMovingUp[2], movingDown: isMovingDown[2] }"
      >
        <img src="../assets/03_img.jpg" />
      </li>
      <li
        v-bind:style="styles[3]"
        v-bind:class="{ movingUp: isMovingUp[3], movingDown: isMovingDown[3] }"
      >
        <img src="../assets/04_img.png" />
      </li>
      <li
        v-bind:style="styles[4]"
        v-bind:class="{ movingUp: isMovingUp[4], movingDown: isMovingDown[4] }"
      >
        <img src="../assets/05_img.jpg" />
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
      pos: [0, 1, 2, 1, 0],
      top: [2, 1, 0, 1, 2],
      animationState: [0, 1, 2, 3, 4],
      imageSelected: 0,
      x_offset: 100,
      y_offset: 30,
      show: false,
      isMovingDown: [false, false, false, false, false],
      isMovingUp: [false, false, false, false, false],
      animationImage: 0
    };
  },
  mounted() {
    const imagesLi = this.$refs.slider.querySelectorAll("li");
    imagesLi.forEach((img, i) => {
      const left = (img.offsetWidth - this.x_offset) * i;
      const top = this.y_offset * this.top[i];
      this.initConfig[i] = this.getStyle(left, top, this.pos[i]);
      this.styles.push(this.getStyle(left, top, this.pos[i]));
    });

    this.show = true;
  },
  methods: {
    getStyle(left, top, zIndex) {
      return {
        transform: `translate(${left}px, ${top}px)`,
        "z-index": zIndex
      };
    },
    animationConfig(element, styleObjElement, delay) {
      this.styles.splice(element, 1, styleObjElement);
      this.initConfig.splice(element, 1, styleObjElement);
      if (delay === "up") {
        this.isMovingUp.splice(element, 1, true);
      } else {
        this.isMovingDown.splice(element, 1, true);
      }
    },
    animationMovingUp() {
      let config = this.initConfig.slice();
      config.forEach((element, i) => {
        if (i === config.length - 1) {
          const animation = () => {
            this.animationConfig(
              i,
              config[0],
              "down"
            );
          }
          setTimeout(animation, 100 * i);

        } else {
          const animation = () => {
            this.animationConfig(
              i,
              config[i + 1],
              "down"
            );
          }
          setTimeout(animation, 100 * i);
        }
      });
    },

    changeImage: function(index) {
      this.imageSelected = index;
      this.isMovingDown=[false, false, false, false, false];
      this.isMovingUp=[false, false, false, false, false];
      if (index < this.initConfig.length - 1) {
        this.animationMovingUp(index);
      } else if (index === this.initConfig.length - 1) {
        this.animationMovingUp(index);
      } else if (index === this.initConfig.length) {
      }
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
    isMovingDown:{
      deep:true,
      handler: function (value){
        // eslint-disable-next-line no-console
        console.log("CAMBIO DE isMovingDown ", value);
      }
    },
    isMovingUp:{
      deep:true,
      handler: function (value){
        // eslint-disable-next-line no-console
        console.log("CAMBIO DE isMovingUp ", value);
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
    .image0 {
      transform: translate("0px", "0px");
      z-index: 2;
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
}
</style>