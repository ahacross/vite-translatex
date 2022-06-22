<template>
  <!-- <div ref="translateSwiper" class="translate-swiper" draggable="true"> -->
  <!-- 위에 주석 처럼 draggable 추가하고 drag event 걸어주면 기능 다시 수정해야 함...-->
  <div ref="translateSwiper" class="translate-swiper">
    <div
      class="slide-wrapper"
      :style="`transform: translateX(${wrapperInfo[0].translate}px)`"
    >
      <button>Slide 1</button>
      <!--      <button>Slide 슬라이드 2</button>-->
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
      <button>Slide 슬라 3</button>
      <button>Slide 4</button>
      <button>Slide 슬라이 5</button>
      <button>Slide 1</button>
      <button>Slide 슬라이드 2</button>
      <button>Slide 슬라 3</button>
      <button>Slide 4</button>
      <button>Slide 슬라이 5</button>
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
        wrapperInfo: [{ translate: 0 }, { translate: 0 }, { translate: 0 }],
        start: 0,
        end: 0,
        max: 0,
        mainIdx: null
      },
      bodyPadding: 16,
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
    limitStart() {
      return innerWidth / 2 - this.bodyPadding
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
      this.swiperInfo.max = swiperMax
      this.swiperInfo.end = swiperMax - translateSwiper.offsetWidth
      this.swiperInfo.mainIdx = this.wrapperInfo.findIndex(({ isMain: m }) => m)
      this.wrapperInfo.forEach(wrapper => {
        wrapper.isMain = wrapper.width / swiperMax === 1
        wrapper.gap = swiperMax - wrapper.width
      })
      this.impetus = new Impetus({
        source: translateSwiper,
        update: x => {
          this.setTranslateX(x)
        },
        boundX: [-this.swiperInfo.end, 0]
      })
    },
    getLeft() {
      const { mainIdx: idx } = this.swiperInfo
      return this.swiper.children[idx].getBoundingClientRect().left
    },
    setTranslateX(changeTranslate) {
      const { start, end } = this.swiperInfo
      const wrapperInfo = this.wrapperInfo
      const left = this.getLeft()
      let ratio = Math.abs((left - this.bodyPadding) / end)
      ratio = ratio > 1 ? 1 : ratio

      // 화면보다 왼쪽으로 더 갔을 때
      if (changeTranslate > start) {
        if (changeTranslate < this.limitStart) {
          wrapperInfo.forEach(wrapper => {
            wrapper.translate = changeTranslate
          })
        }
      } else {
        const { translate: mainTranslate } = this.wrapperMain
        const direction = mainTranslate >= changeTranslate ? 'left' : 'right'

        if ([null, direction].includes(this.beforeDirection)) {
          wrapperInfo.forEach(wrapper => {
            wrapper.translate = changeTranslate + wrapper.gap * ratio
          })
        }
        this.beforeDirection = direction
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
