<template>
  <!-- <div ref="translateSwiper" class="translate-swiper" draggable="true"> -->
  <!-- 위에 주석 처럼 draggable 추가하고 drag event 걸어주면 기능 다시 수정해야 함...-->
  <div ref="translateSwiper" class="translate-swiper">
    <div
      class="slide-wrapper"
      :style="`transform: translateX(${wrapperInfo[0].translate}px)`"
    >
      <button>Slide 1</button>
      <button>Slide 슬라이드 2</button>
      <button>Slide 슬라 3</button>
      <button>Slide 4</button>
      <button>Slide 슬라이 5</button>
      <button>Slide 1</button>
      <button>Slide 슬라이드 2</button>
      <button>Slide 슬라 3</button>
      <button>Slide 4</button>
      <button>Slide 슬라이 5</button>
    </div>
    <div
      class="slide-wrapper"
      :style="`transform: translateX(${wrapperInfo[1].translate}px)`"
    >
      <button>Slide 22</button>
      <button>Slide 23</button>
      <button>Slide 24</button>
      <button>Slide 25</button>
      <button>Slide 22</button>
      <button>Slide 23</button>
      <button>Slide 24</button>
      <button>Slide 25</button>
    </div>
    <div
      class="slide-wrapper"
      :style="`transform: translateX(${wrapperInfo[2].translate}px)`"
    >
      <button>Slide 32</button>
      <button>Slide 33</button>
      <button>Slide 34</button>
      <button>Slide 35</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'translateX',
  data() {
    return {
      swiperInfo: {
        wrapperInfo: [{ translate: 0 }, { translate: 0 }, { translate: 0 }],
        start: 0,
        end: 0,
        correction: 0,
        max: 0
      },
      translateStart: 0
    }
  },
  computed: {
    wrapperInfo() {
      return this.swiperInfo.wrapperInfo
    }
  },
  mounted() {
    this.init()
  },
  methods: {
    init() {
      const translateSwiper = this.$refs.translateSwiper
      // translateSwiper.addEventListener('dragstart', this.onTouchStart)
      // translateSwiper.addEventListener('drag', this.onTouchmove)
      // translateSwiper.addEventListener('dragend', this.onTouchEnd)
      translateSwiper.addEventListener('touchstart', this.onTouchStart)
      translateSwiper.addEventListener('touchmove', this.onTouchmove)
      translateSwiper.addEventListener('touchend', this.onTouchEnd)
      translateSwiper
        .querySelectorAll('.slide-wrapper')
        .forEach((node, idx) => {
          this.wrapperInfo[idx].width = node.offsetWidth
        })
      this.swiperInfo.max = Math.max(
        ...this.wrapperInfo.map(({ width }) => width)
      )
      this.swiperInfo.end =
        this.swiperInfo.max -
        translateSwiper.offsetWidth +
        this.swiperInfo.correction
      this.wrapperInfo.forEach(wrapper => {
        wrapper.isMain = wrapper.width / this.swiperInfo.max === 1
        wrapper.gap = this.swiperInfo.max - wrapper.width
      })
    },
    onTouchStart(evt) {
      this.translateStart =
        this.getChangeTranslate(evt, 'touches') +
        this.wrapperInfo.filter(({ isMain }) => isMain)[0].translate * -1
    },
    onTouchmove(evt) {
      const changeTranslate = this.getChangeTranslate(evt)

      // 화면보다 왼쪽으로 더 갔을 때
      if (changeTranslate > this.swiperInfo.start) {
        this.wrapperInfo.forEach(wrapper => {
          wrapper.translate = changeTranslate
        })
      } else if (changeTranslate < -this.swiperInfo.end) {
        // 화면보다 오른쪽으로 더 갔을 때
        this.wrapperInfo.forEach(wrapper => {
          wrapper.translate = this.calcTranslateX(changeTranslate, wrapper)
        })
      } else {
        this.wrapperInfo.forEach(wrapper => {
          wrapper.translate = this.calcTranslateX(changeTranslate, wrapper)
        })
      }
    },
    onTouchEnd(evt) {
      const changeTranslate = this.getChangeTranslate(evt)

      this.translateStart = null
      // 화면보다 왼쪽으로 더 갔을 때
      if (changeTranslate > this.swiperInfo.start) {
        this.wrapperInfo.forEach(wrapper => {
          wrapper.translate = this.swiperInfo.start
        })
      } else if (changeTranslate < -this.swiperInfo.end) {
        // 화면보다 오른쪽으로 더 갔을 때
        this.wrapperInfo.forEach(wrapper => {
          wrapper.translate = this.calcTranslateX(
            -this.swiperInfo.end,
            wrapper,
            true
          )
        })
      }
    },

    getChangeTranslate(evt, target = 'changedTouches') {
      let pageX
      if (evt.type.includes('drag')) {
        pageX = evt.pageX
      } else {
        pageX = evt[target][0].pageX
      }
      return (this.translateStart - pageX) * -1
    },
    getRect() {
      return this.$refs.translateSwiper.children[
        this.wrapperInfo.findIndex(({ isMain }) => isMain)
      ].getBoundingClientRect()
    },
    calcTranslateX(changeTranslate, wrapper, isEnd) {
      if (wrapper.isMain) {
        return changeTranslate
      } else {
        const left = this.getRect().left
        const ratio = isEnd ? 1 : Math.abs(left / this.swiperInfo.end)
        return changeTranslate + wrapper.gap * (ratio > 1 ? 1 : ratio)
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
    & button {
      white-space: nowrap;
      font-size: 30px;
    }
  }
}
</style>
