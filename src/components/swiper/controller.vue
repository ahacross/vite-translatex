<template>
  <div>
    <div class="swiper swiper1">
      <!-- Additional required wrapper -->
      <div class="swiper-wrapper">
        <!-- Slides -->
        <div class="swiper-slide">Slide 1</div>
        <div class="swiper-slide">Slide 슬라이드 2</div>
        <div class="swiper-slide">Slide 슬라 3</div>
        <div class="swiper-slide">Slide 4</div>
        <div class="swiper-slide">Slide 슬라이 5</div>
      </div>
    </div>
    <div class="swiper swiper2">
      <!-- Additional required wrapper -->
      <div class="swiper-wrapper">
        <!-- Slides -->
        <div class="swiper-slide">Slide 2</div>
        <div class="swiper-slide">Slide 슬라 2</div>
        <div class="swiper-slide">Slide 슬라이드 3</div>
        <div class="swiper-slide">Slide 슬 4</div>
      </div>
    </div>
  </div>
</template>

<script>
import Swiper from 'swiper'

export default {
  name: 'Home',
  data() {
    return {
      swiperOptions: {
        loop: false,
        slidesPerView: 'auto',
        centeredSlides: false,
        freeMode: true,
        grabCursor: true,
        // longSwipes: false,
        // longSwipesMs: 20,
        observer: true, // 추가
        observeParents: true, // 추가
        on: {
          // onAny(eventName, ...args) {
          //   console.log('Event: ', eventName, args);
          // },
          // setTransition(...args) {
          //   console.log('setTransition', args);
          // },
          sliderMove(...args) {
            console.log('sliderMove', args, this, this.getTranslate())
            const translate = this.getTranslate()
            if (translate < 0) {
              this.setTransition(0)
            }
          },
          slideChange() {
            console.log('slideChange')
          },
        },
      },
    }
  },
  mounted() {
    this.init()
  },
  methods: {
    init() {
      const swiper1 = new Swiper('.swiper1', this.swiperOptions)
      const swiper2 = new Swiper('.swiper2', this.swiperOptions)

      swiper1.controller.control = swiper2
      swiper2.controller.control = swiper1

      // this.swiper1 = swiper1;
      // this.swiper2 = swiper2;

      console.log(swiper1, swiper2)
    },
  },
}
</script>
<style scoped>
@import '~swiper/css/swiper.css';
.swiper-slide {
  padding: 10px 20px;
  width: fit-content;
}
</style>
