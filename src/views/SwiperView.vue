<template>
  <div class="swiper">
    <Swiper />
    <br />
    <br />
    <br />
  </div>
</template>

<script setup>
import Swiper from '@/components/swiper/Swiper.vue'
</script>

<style scoped lang="scss">
.swiper {
  :deep(.translate-swiper) {
    width: calc(100%);
    overflow: hidden;
  }
}
</style>
