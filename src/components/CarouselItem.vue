<template>
  <div class="item" :style="styles">
    <slot></slot>
  </div>
</template>

<script>
export default {
  componentName: "carousel-item",
  name: "Carousel",
  data() {
    return {
      height: "auto",
      width: 0
    };
  },
  computed: {
    styles() {
      return {
        width: `${this.width}px`,
        height: `${this.height}`
      };
    }
  },
  watch: {
    height(val) {
        // this.height = val;
    }
  }
};
</script>

<style lang="css" scoped>
.item {
  float: left;
  height: 100%;
  min-height: 1px;
  display: block;
}
</style>