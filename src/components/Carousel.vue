<template>
  <div
    class="layout"
    @touchmove="touchmoveHandler"
    @touchstart="touchstartHandler"
    @touchend="touchendHandler"
  >
    <div class="list">
      <div class="track" :style="trackStyle" :class="{animate: isTransition}">
        <slot></slot>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Carousel",
  data() {
    return {
      slides: [],
      slideInstances: [],
      listWidth: 0,
      trackWidth: "auto",
      trackOffset: 0,
      startPosition: 0,
      index: 1,
      cache: 0,
      isTransition: false,
      canMove: true
    };
  },
  computed: {
    trackStyle() {
      return {
        width: `${this.trackWidth}px`,
        transform: `translate3d(${this.trackOffset}px, 0px, 0px)`
        // transition: 'transform .01s ease-in-out'
      };
    }
  },
  props: {
    height: {
      type: [String, Number],
      default: "auto",
      validator(value) {
        return (
          value === "auto" ||
          Object.prototype.toString.call(value) === "[object Number]"
        );
      }
    }
  },
  watch: {
    height() {
      console.log(this.height);
    },
    trackOffset(val) {
      return val;
    },
    index(val) {
      if (val > this.slideInstances.length) {
        this.index = 1;
      } else if (val < 1) {
        this.index = 1;
      }
    }
  },
  methods: {
    prev() {
      this.canMove = false;
      this.index--;
      let postion = this.index * this.listWidth;
      this.trackOffset = -postion;
      this.$nextTick(() => {
        setTimeout(() => {
          this.isTransition = false;
          this.canMove = true;
        }, 500);
      });
    },
    next() {
      this.canMove = false;
      let postion = this.index * this.listWidth;
      this.trackOffset = -postion;
      this.$nextTick(() => {
        setTimeout(() => {
          this.isTransition = false;
          this.canMove = true;
        }, 500);
      });
      this.index++;
    },
    bouceBack() {
      this.canMove = false;
      this.trackOffset = this.cache;
      this.$nextTick(() => {
        setTimeout(() => {
          this.isTransition = false;
          this.canMove = true;
        }, 500);
      });
    },
    touchendHandler() {
      if (!this.canMove) return;
      // 判断是否超过一半
      let endPosition = event.changedTouches[0].clientX;
      let distance = endPosition - this.startPosition;
      let half = this.listWidth / 4;
      let move = 0;
      this.isTransition = true;

      if (distance < 0 && -distance >= half) {
        if (this.index === this.slideInstances.length) {
          // 如果已经到达了末尾
          this.bouceBack();
        } else {
          this.next();
        }
      } else if (distance > 0 && distance >= half) {
        if (this.index === 0) {
          // 如果已经到达了末尾
          this.bouceBack();
        } else {
          this.prev();
        }
      } else {
        this.bouceBack();
      }
    },
    touchmoveHandler(event) {
      if (!this.canMove) return;
      let nowPosition = event.changedTouches[0].clientX;
      let distance = nowPosition - this.startPosition;
      let move = this.cache + distance;
      this.trackOffset = move;
    },
    touchstartHandler(event) {
      if (!this.canMove) return;
      this.startPosition = event.changedTouches[0].clientX;
      this.cache = this.trackOffset;
    },
    debounce(fn, wait) {
      let timer = null;
      return function(...args) {
        let context = this;
        if (timer) clearTimeout(timer);

        timer = setTimeout(() => {
          fn.apply(context, args);
        }, wait);
      }
    },
    throttle(fn, wait) {
      let timer = null;
      return function(...args) {
        let context = this;
        if (!timer) {
          timer = setTimeout(() => {
            fn.apply(context, args);
            timer = null;
          }, wait || 0);
        }
      };
    },
    findChild() {
      let slides = [];
      this.$children.forEach(child => {
        const name = child.$options.componentName;
        slides.push({
          $el: name.$el
        });
        this.slideInstances.push(child);
      });
      this.slides = slides;
    },
    init() {
      this.findChild();

      const computed = document.defaultView.getComputedStyle(this.$el, "");
      // 计算父容器的宽度
      this.listWidth = parseInt(computed["width"]);
      // 将父元素的宽度设置为轮播图实例的个数*单个宽度
      this.trackWidth = this.listWidth * this.slideInstances.length;

      this.slideInstances.forEach(child => {
        child.width = this.listWidth;
        child.height =
          typeof this.height === "number" ? `${this.height}px` : this.height;
      });
    }
  },
  mounted() {
    this.init();
    // this.next = this.debounce(this.next, 300);
  }
};
</script>

<style>
.layout {
  overflow: hidden;
  position: relative;
  display: block;
  box-sizing: border-box;
}
.list {
  position: relative;
  overflow: hidden;
  margin: 0;
  padding: 0;
}
.track {
  position: relative;
  top: 100%;
  left: 0;
  overflow: hidden;
}
.animate {
  transition: transform 600ms ease-in-out;
}
</style>