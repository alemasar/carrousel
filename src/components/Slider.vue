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
    // const numImages = imagesLi.length;
    //let dir = 1;

    imagesLi.forEach((img, i) => {
      const left = (img.offsetWidth - this.x_offset) * i;
      const top = this.y_offset * this.top[i];
      this.initConfig[i] = {};
      this.initConfig[i].style = {
        top: top,
        left: left,
        "z-index": this.pos[i]
      };
      this.styles.push(this.getStyle(left, top, this.pos[i]));
    });

    this.show = true;
  },
  methods: {
    getStyle(left, top, zIndex) {
      // eslint-disable-next-line no-console
      console.log(left);
      return {
        transform: `translate(${left}px, ${top}px)`,
        "z-index": zIndex
      };
    },
    animationConfig(element, styleObjElement, delay) {
      // eslint-disable-next-line no-console
      console.log(this.getStyle(...styleObjElement));
      this.styles.splice(element, 1, this.getStyle(...styleObjElement));
      //this.initConfig.splice(element, 1, this.getStyle(...styleObjElement));
      if (delay === "up") {
        this.isMovingUp.splice(element, 1, true);
      } else {
        this.isMovingDown.splice(element, 1, true);
      }
    },
    animationMovingUp(index) {
      /*this.animationConfig(index, [
        this.initConfig[index].style.left - this.x_offset,
        this.initConfig[this.pos[2]].style.top,
        this.pos[2]
      ]);*/
      //setTimeout(() => {
      // eslint-disable-next-line no-console
      console.log("FIRST ANIMATION FINISHED", index);

      /*this.isMovingUp[index] = false;
      this.isMovingDown[index] = false;*/
      //let styles = [];

      this.initConfig.forEach((element, i) => {
        if (i === this.initConfig.length - 1) {
          setTimeout(() => {
            this.animationConfig(
              i,
              [
                this.initConfig[0].style.left,
                this.initConfig[this.pos[0]].style.top,
                this.pos[0]
              ],
              "down"
            );
            
          }, 100 * i);

        } else {
          setTimeout(() => {
            this.animationConfig(
              i,
              [
                this.initConfig[i + 1].style.left,
                this.initConfig[this.pos[i + 1]].style.top,
                this.pos[i + 1]
              ],
              "down"
            );
          }, 100 * i);
        }
      });

      //setTimeout(() => {
      /*this.animationConfig(
        3,
        [
          this.initConfig[4].style.left,
          this.initConfig[this.pos[4]].style.top,
          this.pos[4]
        ],
        "down"
      );
      this.animationConfig(
        4,
        [
          this.initConfig[0].style.left,
          this.initConfig[this.pos[0]].style.top,
          this.pos[0]
        ],
        "down"
      );
      this.animationConfig(
        0,
        [
          this.initConfig[1].style.left,
          this.initConfig[this.pos[1]].style.top,
          this.pos[1]
        ],
        "down"
      );*/
      // },2500);
      // eslint-disable-next-line no-console
      console.log(this.styles);
      // setTimeout(() => {
      // eslint-disable-next-line no-console
      console.log("thirst ANIMATION INIT");
      /* styles = [];
          this.animationState.forEach(ind => {
            if (ind === index + 1) {
              styles.push(
                this.getStyle(
                  this.initConfig[index + 1].style.left,
                  this.initConfig[this.pos[index + 1]].style.top,
                  this.pos[index + 1]
                )
              );
            } else {
              styles.push(
                this.getStyle(
                  this.initConfig[ind].style.left,
                  this.initConfig[this.pos[ind]].style.top,
                  this.pos[ind]
                )
              );
            }
          });
          this.styles = styles;
          this.isMovingUp[index + 1] = true;*/
      /*this.animationConfig(index + 1, [
          this.initConfig[2].style.left,
          this.initConfig[this.pos[2]].style.top,
          this.pos[2]
        ]);*/
      // }, 2500);
      //this.isMoving[index] = true;
      //}, 5000);
    },

    changeImage: function(index) {
      this.imageSelected = index;
      this.isMovingDown=[false, false, false, false, false];
      this.isMovingUp=[false, false, false, false, false];
      if (index < this.initConfig.length - 1) {
        //this.animationState[index] = index + 1;
        this.animationMovingUp(index);
      } else if (index === this.initConfig.length - 1) {
        //this.animationState[this.isMoving.length - 1] = 0;
        this.animationMovingUp(index);
      } else if (index === this.initConfig.length) {
        //this.animationState[0] = 1;
      }

      /*    animationEnd:function (){
        
        // eslint-disable-next-line no-console
        console.log("ANIMATION END")
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