<template>
  <div class="slider-container">
    <div class="selected-image-mask"></div>
    <ul ref="slider" class="slider">
      <li
        v-for="(image, index) in images"
        :key="index"
        :style="[position[index],  zIndex[index], background[index], transformAni[index]]"
      >
        <!--{{animation[index]}}-->
        <img :style="[opacity[index], opacityAni[index]]" :src="getImgUrl(image)" />
      </li>
    </ul>
    <ul class="slider-navigator">
      <li
        v-for="(image, indexImg) in images"
        :key="indexImg"
        v-on:click="changeImage(indexImg)"
        :class="activeImage(indexImg)"
      >
        {{indexImg}}
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
      images: [
        "01_img.png",
        "02_img.jpg",
        "03_img.jpg",
        "04_img.png"
        //        "05_img.jpg"
      ],
      animation: [
        {
          pos: 0,
          opacity: 0.5,
          top: 1,
          left: 0,
          scaleX: 0.8,
          scaleY: 0.8,
          zIndex: [1, 2]
        },
        {
          pos: 1,
          opacity: 1,
          top: 0,
          left: 1,
          scaleX: 1,
          scaleY: 1,
          zIndex: [3, 3]
        },
        // { pos: 4, opacity: 0.5, top: 2, left: 0, zIndex: 0 },
        {
          pos: 2,
          opacity: 0.5,
          top: 1,
          left: 2,
          scaleX: 0.8,
          scaleY: 0.8,
          zIndex: [2, 1]
        },
        {
          pos: 3,
          opacity: 0.5,
          top: 0,
          left: 1,
          scaleX: 1,
          scaleY: 1,
          zIndex: [-1, -1]
        }
      ],
      position: [],
      opacity: [],
      top: [1, 0, 1, 0],
      x_offset: 100,
      y_offset: 30,
      zIndex: [],
      imageSelected: 1,
      transformAni: [{}, {}, {}, {}],
      opacityAni: [{}, {}, {}, {}],
      background: [],
      delay: 0.4,
      delayBetweenFrames: 0.2,
      delayBetweenImages: 0,
      moving: false
    };
  },
  mounted() {
    const imagesLi = this.$refs.slider.querySelectorAll("li");
    imagesLi.forEach((img, i) => {
      const left = (img.offsetWidth - this.x_offset) * this.animation[i].left;
      const top = this.y_offset * this.animation[i].top;
      const scaleX = this.animation[i].scaleX;
      const scaleY = this.animation[i].scaleY;
      this.animation[i].left = left;
      this.animation[i].top = top;
      this.position.push(this.getTransform(left, top, scaleX, scaleY));
      //this.scale.push(this.getScale(scaleX, scaleY));
      this.opacity.push(this.getOpacity(this.animation[i].opacity));
      // eslint-disable-next-line no-console
      console.log(this.animation[i].zIndex);
      this.zIndex.push(this.getzIndex(this.animation[i].zIndex[1]));
      this.background.push(this.getBackgroundColor("#fff"));
    });
  },
  methods: {
    activeImage: function(index) {
      return {
        active: this.imageSelected === index
      };
    },
    getImgUrl(image) {
      return require("../assets/" + image);
    },
    getTransform(left, top, scaleX, scaleY) {
      return {
        transform: `translate(${left}px, ${top}px) scale(${scaleX}, ${scaleY})`
      };
    },
    getOpacity(opacity) {
      return {
        opacity: opacity
      };
    },
    getzIndex(zIndex) {
      return { "z-index": zIndex };
    },
    getBackgroundColor(color) {
      return { "background-color": color };
    },
    getTransitionAni(delay) {
      return { transition: `transform ${delay}s ease-in-out` };
    },
    getOpacityAni(delay) {
      return { transition: `opacity ${delay}s ease-in-out` };
    },
    beforeMove(dir) {
      let todoList = this.animation.slice();
      this.animation = [];
      todoList.forEach((element, i) => {
        let pos = 0;

        if (i === 0 && dir === -1) {
          pos = todoList.length - 1;
        } else if (i === todoList.length - 1 && dir === 1) {
          pos = 0;
        } else {
          pos = i + dir;
        }
        let nextElement = {};
        nextElement.pos = pos;
        nextElement.left = todoList[pos].left;
        nextElement.top = todoList[pos].top;
        nextElement.scaleX = todoList[pos].scaleX;
        nextElement.scaleY = todoList[pos].scaleY;
        nextElement.opacity = todoList[pos].opacity;
        nextElement.zIndex = todoList[pos].zIndex;
        this.animation.push(nextElement);
      });
      this.makeAnimation(dir);
    },
    makeAnimation(dir) {
      this.animation.forEach((element, i) => {
        // eslint-disable-next-line no-console
        console.log("Pos inicial " + element.pos + " pos final " + i);
        let zIndex = 0;
        if (dir < 0) {
          zIndex = 1;
        }
        const animate = () => {
          this.position.splice(
            i,
            1,
            this.getTransform(
              element.left,
              element.top,
              element.scaleX,
              element.scaleY
            )
          );
          this.opacity.splice(i, 1, this.getOpacity(element.opacity));
          this.transformAni.splice(i, 1, this.getTransitionAni(this.delay));
          this.opacityAni.splice(i, 1, this.getOpacityAni(this.delay));
          // eslint-disable-next-line no-console
          console.log(i + ":  left: " + element.left + "  top: " + element.top);
          this.zIndex.splice(i, 1, this.getzIndex(element.zIndex[zIndex]));
          //const onFinish = () => {
          this.background.splice(
            element.pos,
            1,
            this.getBackgroundColor("#fff")
          );
          //};
          //setTimeout(onFinish, this.delay * 1000);
        };
        if (this.delayBetweenImages > 0) {
          setTimeout(() => {
            animate();
          }, this.delayBetweenImages * i);
        } else {
          animate();
        }
      });
    },
    move: function(animation) {
      const onFinish = () => {
        const ani = animation.shift();
        if (ani) {
          ani();
          setTimeout(() => {
            this.move(animation);
          }, this.delayBetweenFrames * 1000);
        } else {
        // eslint-disable-next-line no-console
        console.log("fin animacion");
        this.moving = false;
        }
      };
      setTimeout(onFinish, this.delay * 1000);
    },
    changeImage: function(index) {
      if (!this.moving) {
        this.moving = true;
        let dir = 1;
        let dist = this.imageSelected - index;

        if (dist !== 0) {
          if (dist < 0) {
            dir = -1;
            dist *= -1;
          }
          /*setTimeout(()=>{
          dir*/
          // eslint-disable-next-line no-unused-vars
          const animationFrames = Array.from({ length: dist }, x => () => {
            this.beforeMove(dir);
          });
          // eslint-disable-next-line no-console
          console.log(animationFrames);
          this.move(animationFrames);
          //}, 3000)

          this.imageSelected = index;
        }
      }
    }
  }
};
</script>
<style lang="scss" scoped>
.slider-container {
    position: relative;
    overflow: hidden;
    width:670px;
  div.selected-image-mask{
    background: transparent url(../assets/selected-image-mask.png) no-repeat top left;
    background-size: 270px 380px;
    top: -2px;
    left:50%;
    transform: translateX(-50%);
    width:300px;
    height:600px;
    position:absolute;
    z-index:100;
  }
  ul.slider {
    display: flex;
    list-style: none;
    position: relative;
    height: 330px;


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
      &.active {
        background-color: red;
      }
    }
  }
}
</style>