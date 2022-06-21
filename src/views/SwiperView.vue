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
    <br />
    <br />
    <br />
    <SwiperTranslateXImpetus />
  </div>
</template>

<script setup>
import SwiperTranslateX from '@/components/swiper/translatex.vue'
import SwiperTranslateXCss from '@/components/swiper/translatex-css.vue'
import SwiperTranslateXRe from '@/components/swiper/translatex-re.vue'
import SwiperTranslateXImpetus from '@/components/swiper/translatex-impetus.vue'
</script>

<style scoped lang="scss">
.swiper {
  :deep(.translate-swiper) {
    width: calc(100%);
    overflow: hidden;
  }
}
</style>
