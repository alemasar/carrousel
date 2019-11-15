<template>
  <div class="slider-container">
    {{styles[0]}}
    <ul ref="slider" class="slider">
      <li v-bind:style="styles[0]" v-bind:class="{ movingUp: isMovingUp[0], isMoving: isMoving[0]}">
        <img src="../assets/01_img.png" />
      </li>
      <li
        v-bind:style="styles[1]"
        v-bind:class="{ movingUp: isMovingUp[1], isMoving: isMoving[1] }"
      >
        <img src="../assets/02_img.jpg" />
      </li>
      <li
        v-bind:style="styles[2]"
        v-bind:class="{ movingUp: isMovingUp[2], isMoving: isMoving[2] }"
      >
        <img src="../assets/03_img.jpg" />
      </li>
      <li
        v-bind:style="styles[3]"
        v-bind:class="{ movingUp: isMovingUp[3], isMoving: isMoving[3] }"
      >
        <img src="../assets/04_img.png" />
      </li>
      <li
        v-bind:style="styles[4]"
        v-bind:class="{ movingUp: isMovingUp[4], isMoving: isMoving[4] }"
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
      images: [],
      styles: [],
      pos: [0, 1, 2, 1, 0],
      top: [2, 1, 0, 1, 2],
      animationState: [0, 1, 2, 3, 4],
      imageSelected: 0,
      x_offset: 100,
      y_offset: 30,
      show: false,
      isMoving: [false, false, false, false, false],
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
      this.images[i] = {};
      this.images[i].style = {
        top: top,
        left: left,
        "z-index": this.pos[i]
      };
      this.styles.push({
        transform: `translate(${left}px, ${top}px)`,
        "z-index": this.pos[i]
      });
    });

    this.show = true;
  },
  methods: {
    animationMovingUp(index) {
      let styles = [];
      this.animationState.forEach(ind => {
        if (ind === index) {
          styles.push({
            transform: `translate(${this.images[index].style.left -
              this.x_offset}px, ${this.images[this.pos[2]].style.top}px)`,
            "z-index": this.pos[ind]
          });
        } else {
          styles.push({
            transform: `translate(${this.images[ind].style.left}px, ${this.images[ind].style.top}px)`,
            "z-index": this.pos[ind]
          });
        }
      });
      this.styles = styles;
      this.isMoving[index] = true;
      this.isMovingUp[index] = true;
      setTimeout(() => {
        // eslint-disable-next-line no-console
        console.log("FIRST ANIMATION FINISHED");
        this.isMovingUp[index] = false;
        this.isMoving[index] = false;
        styles = [];
        this.animationState.forEach(ind => {
          if (ind === index) {
            styles.push({
              transform: `translate(${this.images[2].style.left}px, ${this.images[this.pos[2]].style.top}px)`,
              "z-index": this.pos[ind]
            });
          } else {
            styles.push({
              transform: `translate(${this.images[ind].style.left}px, ${this.images[ind].style.top}px)`,
              "z-index": this.pos[ind]
            });
          }
        });
        // eslint-disable-next-line no-console
        console.log(styles);
        this.styles = styles;
        this.isMovingUp[index] = true;

        setTimeout(() => {
                    // eslint-disable-next-line no-console
        console.log("thirst ANIMATION INIT");
       styles = [];
          this.animationState.forEach(ind => {
            if (ind === index+1) {
              styles.push({
                transform: `translate(${this.images[index+1].style.left}px, ${this.images[this.pos[index+1]].style.top}px)`,
                "z-index": this.pos[index+1]
              });
            } else {
              styles.push({
                transform: `translate(${this.images[ind].style.left}px, ${this.images[ind].style.top}px)`,
                "z-index": this.pos[ind]
              });
            }
          });
          this.styles = styles;
          this.isMovingUp[index+1] = true;
        }, 2500);
        //this.isMoving[index] = true;
      }, 5000);
    },

    changeImage: function(index) {
      this.imageSelected = index;
      if (index < this.isMoving.length - 1) {
        //this.animationState[index] = index + 1;
        this.animationMovingUp(index);
      } else if (index === this.isMoving.length - 1) {
        //this.animationState[this.isMoving.length - 1] = 0;
        this.animationMovingUp(index);
      } else if (index === this.isMoving.length) {
        //this.animationState[0] = 1;
      }

      /*    animationEnd:function (){
        
        // eslint-disable-next-line no-console
        console.log("ANIMATION END")
    }*/
    }
  },
  watch: {
    styles: function() {
      // eslint-disable-next-line no-console
      console.log("CAMBIO DE STILO ");
    },
    deep: true
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
    transition: 5s linear;
  }
}
</style>