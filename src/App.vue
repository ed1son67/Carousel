<template>
  <div id="app">
    <div>demo </div>
    <Carousel :height= '200'>
      <CarouselItem style="background: red">
        1
      </CarouselItem >
       <CarouselItem style="background: blue">
        2
      </CarouselItem>
      <CarouselItem style="background: green">
        3
      </CarouselItem>
      <CarouselItem style="background: pink">
        4
      </CarouselItem>
      <CarouselItem style="background: gray">
        5
      </CarouselItem>
    </Carousel >
  </div>
</template>

<script>
import Carousel from './components/Carousel.vue'
import CarouselItem from './components/CarouselItem.vue'
export default {
  name: 'App',
  components: {
    Carousel,
    CarouselItem
  },
  mounted() {
    (function() {
      let deviceWith = document.documentElement.clientWidth;
      deviceWith = deviceWith < 320 ? 320 : deviceWith > 640 ? 640 : deviceWith;
      document.documentElement.style.fontSize = deviceWith / 7.50 + 'px';
    })();
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  
}
body {
  margin: 0;
  padding: 0;
  font-size: 1rem;
}
</style>
