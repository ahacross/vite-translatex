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
import throttle from 'lodash/throttle'

export default {
  name: 'translateX',
  data() {
    return {
      paddingLeft: 9,
      swiperInfo: {
        wrapperInfo: [{ translate: 0 }, { translate: 0 }, { translate: 0 }],
        start: 0,
        end: 0,
        max: 0
      },
      translateStart: 0,
      events: {}
    }
  },
  computed: {
    wrapperInfo() {
      return this.swiperInfo.wrapperInfo
    },
    wrapperMain() {
      return this.wrapperInfo.filter(({ isMain }) => isMain)[0]
    }
  },
  mounted() {
    this.init()
  },
  methods: {
    addEvent(el, name, fn) {
      el.addEventListener(name, this.setEvent(name, fn))
    },
    removeEvent(el, name) {
      el.removeEventListener(name, this.events[name])
    },
    setEvent(name, fn) {
      this.events[name] = fn
      return fn
    },
    init() {
      const translateSwiper = this.$refs.translateSwiper
      this.removeEvent(translateSwiper, 'touchstart')
      this.removeEvent(translateSwiper, 'touchmove')
      this.removeEvent(translateSwiper, 'touchend')

      this.addEvent(translateSwiper, 'touchstart', this.onTouchStart, false)
      this.addEvent(translateSwiper, 'touchmove', this.onTouchMove, false)
      this.addEvent(translateSwiper, 'touchend', this.onTouchEnd, false)

      translateSwiper
        .querySelectorAll('.slide-wrapper')
        .forEach((node, idx) => {
          this.wrapperInfo[idx].width = node.offsetWidth
        })
      const swiperMax = Math.max(...this.wrapperInfo.map(({ width }) => width))
      this.swiperInfo.max = swiperMax
      this.swiperInfo.end = swiperMax - translateSwiper.offsetWidth
      this.wrapperInfo.forEach(wrapper => {
        wrapper.isMain = wrapper.width / swiperMax === 1
        wrapper.gap = swiperMax - wrapper.width
      })
    },
    onTouchStart(evt) {
      this.translateStart =
        this.getChangeTranslate(evt, 'touches') +
        this.wrapperMain.translate * -1 -
        this.paddingLeft
      // padding left가 8임
    },
    onTouchMove(evt) {
      this.setTranslateX(this.getChangeTranslate(evt))
    },
    onTouchEnd(evt) {
      this.setTranslateX(this.getChangeTranslate(evt), true)
      this.translateStart = null
    },
    getChangeTranslate(evt, target = 'changedTouches') {
      const { pageX } = evt[target][0]
      return (this.translateStart - pageX) * -1
    },
    getDirection() {
      this.wrapperInfo.filter(({ isMain }) => isMain)[0].translate
    },
    setTranslateX(changeTranslate, isEnd = false) {
      const { start, end } = this.swiperInfo
      const wrapperInfo = this.wrapperInfo
      const { left } =
        this.$refs.translateSwiper.children[
          wrapperInfo.findIndex(({ isMain }) => isMain)
        ].getBoundingClientRect()

      // 화면보다 왼쪽으로 더 갔을 때
      if (changeTranslate > start) {
        wrapperInfo.forEach(wrapper => {
          wrapper.translate = isEnd ? start : changeTranslate
        })
      } else if (changeTranslate < -end || !isEnd) {
        changeTranslate = isEnd ? -end : changeTranslate
        let translate = changeTranslate

        const direction =
          this.wrapperMain.translate >= changeTranslate ? 'left' : 'right'

        wrapperInfo.forEach(wrapper => {
          if (!wrapper.isMain) {
            const ratio = isEnd ? 1 : Math.abs(left / end)
            translate = changeTranslate + wrapper.gap * (ratio > 1 ? 1 : ratio)
            if (direction === 'left' && translate > wrapper.translate) {
              translate = Number(wrapper.translate)
            } else if (direction === 'right' && translate < wrapper.translate) {
              translate = Number(wrapper.translate)
            }
          }

          wrapper.translate = translate.toFixed(2)
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
