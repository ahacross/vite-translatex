<template>
  <!-- <div ref="translateSwiper" class="translate-swiper" draggable="true"> -->
  <!-- 위에 주석 처럼 draggable 추가하고 drag event 걸어주면 기능 다시 수정해야 함...-->
  <div ref="translateSwiper" class="translate-swiper">
    <div
      class="slide-wrapper"
      :style="`transform: translateX(${wrapperInfo[0].translate}px)`"
    >
      <button>Slide 11</button>
      <button>Slide 12</button>
      <button>Slide 13</button>
      <button>Slide 14</button>
      <button>Slide 15</button>
    </div>
    <div
      class="slide-wrapper"
      :style="`transform: translateX(${wrapperInfo[1].translate}px)`"
    >
      <button>Slide 32</button>
      <button>Slide 33</button>
      <button>Slide 34</button>
      <button>Slide 35</button>
      <button>Slide 36</button>
      <button>Slide 37</button>
      <button>Slide 38</button>
      <button>Slide 39</button>
    </div>
  </div>
</template>

<script>
import Impetus from 'impetus'
export default {
  name: 'translateXImpetus',
  data() {
    return {
      swiperInfo: {
        wrapperInfo: [{ translate: 0 }, { translate: 0 }],
        start: 0,
        end: 0,
        max: 0,
        mainIdx: null
      },
      bodyPadding: 16,
      addPadding: 42,
      beforeDirection: null,
      impetus: null
    }
  },
  computed: {
    wrapperInfo() {
      return this.swiperInfo.wrapperInfo
    },
    wrapperMain() {
      return this.wrapperInfo[this.swiperInfo.mainIdx]
    },
    wrapperMainEl() {
      return this.swiper.children[this.swiperInfo.mainIdx]
    },
    limitStart() {
      return innerWidth / 2 - this.bodyPadding
    },
    limitEnd() {
      return innerWidth / 2 - this.bodyPadding + this.swiperInfo.end
    },
    swiper() {
      return this.$refs.translateSwiper
    }
  },
  mounted() {
    this.init()
  },
  beforeUnmount() {
    this.impetus?.destroy()
  },
  methods: {
    init() {
      const translateSwiper = this.swiper

      translateSwiper
        .querySelectorAll('.slide-wrapper')
        .forEach((node, idx) => {
          this.wrapperInfo[idx].width = node.offsetWidth
        })
      const swiperMax = Math.max(...this.wrapperInfo.map(({ width }) => width))
      this.wrapperInfo.forEach(wrapper => {
        wrapper.isMain = wrapper.width / swiperMax === 1
        wrapper.gap = swiperMax - wrapper.width
      })
      this.swiperInfo.max = swiperMax
      this.swiperInfo.end = swiperMax - translateSwiper.offsetWidth
      this.swiperInfo.mainIdx = this.wrapperInfo.findIndex(({ isMain: m }) => m)

      this.impetus = new Impetus({
        source: translateSwiper,
        update: x => {
          this.setTranslateX(x)
        },
        boundX: [-(this.swiperInfo.end + this.addPadding), 0]
      })
    },
    setTranslateX(changeTranslate) {
      const { start, end } = this.swiperInfo
      const wrapperInfo = this.wrapperInfo
      const left = this.wrapperMainEl.getBoundingClientRect().left
      let ratio = Math.abs((left - this.bodyPadding) / end)
      ratio = ratio > 1 ? 1 : ratio
      let func

      // 화면보다 왼쪽으로 더 갔을 때
      if (changeTranslate > start) {
        changeTranslate =
          changeTranslate < this.limitStart ? changeTranslate : this.limitStart
        func = wrapper => {
          wrapper.translate = changeTranslate
        }
      } else if (changeTranslate < -this.limitEnd) {
        // 화면보다 오른쪽으로 더 갔을 때
      } else {
        func = wrapper => {
          wrapper.translate = changeTranslate + wrapper.gap * ratio
        }
      }
      func && wrapperInfo.forEach(func)
    }
  }
}
</script>
<style scoped lang="scss">
.translate-swiper {
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
  overflow: hidden;
  -webkit-touch-callout: none;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  & .slide-wrapper {
    display: flex;
    width: fit-content;
    transition-duration: 0ms;
    transition-property: transform;
    transform-origin: 100% 100%;
    & button {
      white-space: nowrap;
      font-size: 30px;
    }
  }
}
</style>
