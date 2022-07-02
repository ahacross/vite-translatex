<template>
  <div v-swiper="swiperOptions" class="swiper" ref="swiper">
    <!-- Additional required wrapper -->
    <div class="swiper-wrapper" :class="{ 'tab-right': isEnd }">
      <!-- Slides -->
      <div class="swiper-slide">Slide 1</div>
      <div class="swiper-slide">Slide 이건 2</div>
      <div class="swiper-slide">Slide 3</div>
      <div class="swiper-slide">Slide 뭐셔 4</div>
      <div class="swiper-slide">Slide 모르지~ 5</div>
      <div class="swiper-slide">Slide 6</div>
      <div class="swiper-slide">Slide 나는 모름 7</div>
      <div class="swiper-slide">Slide 8</div>
      <div class="swiper-slide">Slide 하하하하하 9</div>
    </div>
  </div>
</template>

<script>
import { directive } from 'vue-awesome-swiper'

export default {
  name: 'Home',
  directives: {
    swiper: directive,
  },
  data() {
    const self = this
    return {
      isEnd: false,
      swiperOptions: {
        loop: false,
        slidesPerView: 'auto',
        freeMode: true,
        height: 50,
        grabCursor: true,
        slidesPerColumn: 2, //
        slidesPerColumnFill: 'row',
        spaceBetween: 10,
        on: {
          // onAny(eventName, ...args) {
          //   console.log('Event: ', eventName, args);
          // },
          slideChange() {
            console.log('slideChange')
            self.isEnd = this.isEnd
          },
        },
      },
    }
  },
}
</script>
<style scoped>
@import '~swiper/css/swiper.css';
.swiper-slide {
  width: fit-content;
}
.tab-left {
  justify-content: start;
}

.tab-right {
  justify-content: end;
}
</style>
