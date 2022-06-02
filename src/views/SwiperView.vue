<template>
  <div class="swiper">
    <SwiperTranslateX />
    <br />
    <br />
    <br />
    <SwiperTranslateXCss />
    <br />
    <br />
    <br />
    <SwiperTranslateXRe />
    <div style="width: 1px; padding-left: 180.5px">1</div>
  </div>
</template>

<script setup>
import SwiperTranslateX from '@/components/swiper/translatex.vue'
import SwiperTranslateXCss from '@/components/swiper/translatex-css.vue'
import SwiperTranslateXRe from '@/components/swiper/translatex-re.vue'
</script>

<style scoped lang="scss">
.swiper {
  :deep(.translate-swiper) {
    width: calc(100%);
    overflow: hidden;
  }
}
</style>
