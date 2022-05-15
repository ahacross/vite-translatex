<template>
  <!-- <div ref="translateSwiper" class="translate-swiper" draggable="true"> -->
  <!-- 위에 주석 처럼 draggable 추가하고 drag event 걸어주면 기능 다시 수정해야 함...-->
  <div ref="translateSwiper" class="translate-swiper">
    <div
      class="slide-wrapper"
      :style="`transform: translateX(${swiperInfo.rowInfo[0].translateX}px)`"
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
      :style="`transform: translateX(${swiperInfo.rowInfo[1].translateX}px)`"
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
      :style="`transform: translateX(${swiperInfo.rowInfo[2].translateX}px)`"
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
  name: 'translateXCss',
  data() {
    return {
      swiperInfo: {
        start: 0,
        correction: 0,
        translate: 0,
        width: 0,
        rowInfo: [
          { width: 0, translateX: 0 },
          { width: 0, translateX: 0 },
          { width: 0, translateX: 0 }
        ]
      },
      translateStart: 0
      // throttle: {}
    }
  },
  computed: {
    rowInfo() {
      return this.swiperInfo.rowInfo
    }
  },
  mounted() {
    this.init()
  },
  methods: {
    setTranslateX() {
      const { translate, width } = this.swiperInfo
      let ratio = translate / width
      console.log(ratio)
      if (ratio > 0) {
        ratio = 0
      }
      for (const row of this.rowInfo) {
        row.translateX = ratio * row.ratio * row.width
        // console.log(row.translateX)
      }
    },
    addEvent(el, name, fn) {
      el.addEventListener(name, this.setThrottle(name, fn))
    },
    removeEvent(el, name) {
      el.removeEventListener(name, this.throttle[name])
    },
    setThrottle(name, fn, time = 16) {
      this.throttle[name] = throttle(fn, time)
      return this.throttle[name]
    },
    init() {
      const translateSwiper = this.$refs.translateSwiper

      // translateSwiper.addEventListener('dragstart', this.onTouchStart)
      // translateSwiper.addEventListener('drag', this.onTouchmove)
      // translateSwiper.addEventListener('dragend', this.onTouchEnd)

      // this.removeEvent(translateSwiper, 'touchstart')
      // this.removeEvent(translateSwiper, 'touchmove')
      // this.removeEvent(translateSwiper, 'touchend')
      //
      // this.addEvent(translateSwiper, 'touchstart', this.onTouchStart)
      // this.addEvent(translateSwiper, 'touchmove', this.onTouchMove)
      // this.addEvent(translateSwiper, 'touchend', this.onTouchEnd)

      translateSwiper.addEventListener('touchstart', this.onTouchStart)
      translateSwiper.addEventListener('touchmove', this.onTouchMove)
      translateSwiper.addEventListener('touchend', this.onTouchEnd)

      translateSwiper
        .querySelectorAll('.slide-wrapper')
        .forEach((node, idx) => {
          this.rowInfo[idx].width = node.offsetWidth
        })

      this.swiperInfo.width = Math.max(
        ...this.rowInfo.map(({ width }) => width)
      )

      for (const row of this.rowInfo) {
        row.ratio = row.width / this.swiperInfo.width
      }
    },
    getChangeTranslate(evt, target = 'changedTouches') {
      return (this.translateStart - evt[target][0].pageX) * -1
    },
    onTouchStart(evt) {
      this.translateStart =
        this.getChangeTranslate(evt, 'touches') + this.swiperInfo.translate * -1
    },
    onTouchMove(evt) {
      const changeTranslate = this.getChangeTranslate(evt)
      this.swiperInfo.translate = changeTranslate

      if (changeTranslate > this.swiperInfo.start) {
        this.setTranslateX()
      } else {
        this.setTranslateX()
      }
    },
    onTouchEnd(evt) {
      const changeTranslate = this.getChangeTranslate(evt)

      this.translateStart = 0
      // 화면보다 왼쪽으로 더 갔을 때
      if (changeTranslate > this.swiperInfo.start) {
        this.swiperInfo.translate = this.swiperInfo.start
      } else if (
        changeTranslate <
        -(this.swiperInfo.width - this.$refs.translateSwiper.offsetWidth)
      ) {
        // 화면보다 오른쪽으로 더 갔을 때
        this.swiperInfo.translate = -(
          this.swiperInfo.width - this.$refs.translateSwiper.offsetWidth
        )
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
