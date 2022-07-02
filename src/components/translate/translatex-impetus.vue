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
      hasPadding: 31,
      wrapperMain: null,
      wrapperMainEl: null,
      impetus: null
    }
  },
  computed: {
    wrapperInfo() {
      return this.swiperInfo.wrapperInfo
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
      const mainIdx = this.wrapperInfo.findIndex(({ isMain }) => isMain)
      this.swiperInfo.mainIdx = mainIdx
      this.wrapperMain = this.wrapperInfo[mainIdx]
      this.wrapperMainEl = this.swiper.children[mainIdx]

      this.impetus = new Impetus({
        source: translateSwiper,
        multiplier: 1.3,
        update: x => {
          this.setTranslateX(x)
        },
        boundX: [-(this.swiperInfo.end + this.hasPadding), 0]
      })
    },
    getRatio() {
      const { end } = this.swiperInfo
      const left = this.wrapperMainEl.getBoundingClientRect().left
      let ratio = Math.abs(left / end)
      ratio = ratio > 1 ? 1 : ratio
      return ratio
    },
    setTranslateX(changeTranslate) {
      const { start, end } = this.swiperInfo

      if (changeTranslate > start) {
        // 화면보다 왼쪽으로 더 갔을 때
        if (changeTranslate > this.limitStart) {
          changeTranslate = this.limitStart
        }
        this.applyTranslate(changeTranslate)
      } else if (changeTranslate < -end) {
        // 화면보다 오른쪽으로 더 갔을 때
        if (changeTranslate < -this.limitEnd) {
          changeTranslate = -this.limitEnd
          this.applyTranslate(changeTranslate, 1)
        } else {
          console.log(changeTranslate, this.getRatio())
          this.applyTranslate(changeTranslate, this.getRatio())
        }
      } else {
        this.applyTranslate(changeTranslate, this.getRatio())
      }
    },
    applyTranslate(changeTranslate, ratio) {
      const wrapperInfo = this.wrapperInfo

      if (ratio) {
        wrapperInfo.forEach(wrapper => {
          wrapper.translate = changeTranslate + wrapper.gap * ratio
        })
      } else {
        wrapperInfo.forEach(wrapper => {
          wrapper.translate = changeTranslate
        })
      }
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
