<template>
  <div v-swiper:mySwiper="swiperOption">
    <div class="swiper-wrapper">
      <div class="swiper-slide" v-for="slide in swiperSlide" :key="slide">
        {{ slide }}
      </div>
    </div>
    <div class="swiper-pagination"></div>
  </div>
</template>

<script>
export default {
  name: 'testSwiper',
  data: () => ({
    swiperSlide: [
      'slide1',
      'slide2',
      'slide3',
      'slide4',
      'slide5',
      'slide6',
      'slide7',
      'slide8',
      'slide9',
      'slide10'
    ],
    swiperOption: {
      pagination: {
        el: '.swiper-pagination'
      }
    }
  })
}
</script>

<style scoped></style>
