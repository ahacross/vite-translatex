<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <header>
    <div class="wrapper">
      <nav>
        <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/about">About</RouterLink>
        <RouterLink to="/swiper">swiper</RouterLink>
      </nav>
    </div>
  </header>

  <RouterView />
</template>
<style lang="scss">
#app {
}
</style>
<style lang="scss" scoped>
nav {
  a {
    margin-right: 10px;
  }
}
</style>
