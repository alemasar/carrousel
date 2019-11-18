<template>
  <div class="slider-container">
    <ul ref="slider" class="slider">
      <li
        v-for="(image, index) in images"
        :key="index"
        :style="[position[index], zIndex[index], background[index], transformAni[index]]"
      >
        {{animation[index]}}
        <img
          :style="[opacity[index], opacityAni[index]]"
          :src="getImgUrl(image)"
        />
      </li>
    </ul>
    <ul class="slider-navigator">
      <li v-for="(image, indexImg) in images" :key="indexImg" v-on:click="changeImage(indexImg)">
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
        { pos: 0, opacity: 0.5, top: 1, left: 0, scaleX: .5, scaleY: .5, zIndex: [1, 2] },
        { pos: 1, opacity: 1, top: 0, left: 1, scaleX: .5, scaleY: .5, zIndex: [3, 3] },
        // { pos: 4, opacity: 0.5, top: 2, left: 0, zIndex: 0 },
        { pos: 2, opacity: 0.5, top: 1, left: 2, scaleX: .5, scaleY: .5, zIndex: [2, 1] },
        { pos: 3, opacity: 0.5, top: 0, left: 1, scaleX: .5, scaleY: .5, zIndex: [-1, -1] }
      ],
      position: [],
      opacity: [],
      top: [1, 0, 1, 0],
      x_offset: 100,
      y_offset: 30,
      zIndex: [],
      imageSelected: 0,
      transformAni: [{}, {}, {}, {}],
      opacityAni: [{}, {}, {}, {}],
      background: [],
      delay: .4
    };
  },
  mounted() {
    const imagesLi = this.$refs.slider.querySelectorAll("li");
    imagesLi.forEach((img, i) => {
      const left = (img.offsetWidth - this.x_offset) * this.animation[i].left;
      const top = this.y_offset * this.animation[i].top;
      this.animation[i].left = left;
      this.animation[i].top = top;
      this.position.push(this.getPosition(left, top));
      this.opacity.push(this.getOpacity(this.animation[i].opacity));
      // eslint-disable-next-line no-console
      console.log(this.animation[i].zIndex);
      this.zIndex.push(this.getzIndex(this.animation[i].zIndex[1]));
      this.background.push(this.getBackgroundColor("#fff"));
    });
  },
  methods: {
    getImgUrl(image) {
      return require("../assets/" + image);
    },
    getPosition(left, top /*, zIndex*/) {
      return {
        transform: `translate(${left}px, ${top}px)`
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
        nextElement.opacity = todoList[pos].opacity;
        nextElement.zIndex = todoList[pos].zIndex;
        this.animation.push(nextElement);
      });
      this.move(dir);
    },
    move(dir) {
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
            this.getPosition(element.left, element.top)
          );
          this.opacity.splice(i, 1, this.getOpacity(element.opacity));
          this.transformAni.splice(i, 1, this.getTransitionAni(this.delay));
          this.opacityAni.splice(i, 1, this.getOpacityAni(this.delay));
          // eslint-disable-next-line no-console
          console.log(i + ":  left: " + element.left + "  top: " + element.top);
          this.zIndex.splice(i, 1, this.getzIndex(element.zIndex[zIndex]));
          const onFinish = () => {
            /*            if (element.pos === 2 && dir === 1) {
              // eslint-disable-next-line no-console
              //console.log(" if element.pos " + element.pos + " index " + i);
              // this.zIndex.splice(element.pos, 1, this.getzIndex(1));
            }*/
            this.background.splice(
              element.pos,
              1,
              this.getBackgroundColor("#fff")
            );
            //this.changeImage(this.imageSelected + 1)
          };
          setTimeout(onFinish, this.delay*1000);
        };
        //setTimeout(() => {
        animate();
        //}, 100 * i);
      });
    },
    changeImage: function(index) {
      let dir = 1;
      if (index !== 1) {
        if (index < this.imageSelected) {
          dir = -1;
        }
        /*setTimeout(()=>{
          dir*/
        this.beforeMove(dir);
        //}, 3000)

        this.imageSelected = index;
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
    transition: transform 1s ease-in-out;
  }
  .animationImg {
    transition: opacity 1s ease-in-out;
  }
}
</style>