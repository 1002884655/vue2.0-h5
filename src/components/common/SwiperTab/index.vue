<template>
  <div class="components SwiperTab flex-v" :class="`SwiperTabContainer-${RefId}`">
    <div class="Tab">
      <swiper :options="TabOptions" :class="`SwiperTab-${RefId}`" :ref="`SwiperTab-${RefId}`">
        <swiper-slide v-for="(item, index) in TabArr" :key="index">
          <div class="SwiperTab-Item" @click="CutTab(index)" :class="[ShowAnimateLine ? `SwiperTab-Item-${RefId}` : `SwiperTab-Item-${RefId} ShowActiveLine`]">
            <span :class="[ TabActiveIndex === index ? `SwiperTab-Text-${RefId} active` : `SwiperTab-Text-${RefId}`]">{{item.name}}<i :style="{background: ActiveColor}"></i></span>
          </div>
        </swiper-slide>
      </swiper>
    </div>
    <div class="flex-item">
      <div>
        <swiper :options="TabContentOptions" :ref="`SwiperTabContent-${RefId}`">
          <swiper-slide v-for="(item, index) in TabArr" :key="index">
            <slot :name="`TabSwiperSlot-${item.id}`"></slot>
          </swiper-slide>
        </swiper>
      </div>
    </div>
  </div>
</template>

<script>
import 'swiper/dist/css/swiper.css'
import { swiper, swiperSlide } from 'vue-awesome-swiper'
export default {
  name: 'SwiperTab',
  props: {
    RefId: { // 唯一id
      default: `${Date.now()}`,
      type: String
    },
    TabArr: { // tab数据
      default: () => {
        return [{ name: '选项一', id: 1 }, { name: '选项二', id: 2 }, { name: '选项三', id: 3 }, { name: '选项四', id: 4 }]
      },
      type: Array
    },
    ShowLength: { // 可见区域显示tab数量
      default: 4,
      type: Number
    },
    ActiveColor: { // 选中色值
      default: 'red',
      type: String
    },
    ActiveId: { // 默认选中id
      default: null
    },
    AnimateStep: { // 动画间帧数
      default: 100,
      type: Number
    }
  },
  data () {
    const _that = this
    return {
      AnimateArr: [],
      ShowAnimateLine: false,
      TabActiveIndex: null,
      Timer: null,
      TabOptions: {
        initialSlide: 0,
        slidesPerView: 4,
        autoplay: false
      },
      AnimateStepCount: (this.TabArr.length - 1) * this.AnimateStep,
      TabContentOptions: {
        autoplay: false,
        on: {
          init: function () {
            _that.TabActiveIndex = this.activeIndex
          },
          progress: function (swiper) {
            if (document.getElementsByClassName(`TabActiveLine-${_that.RefId}`)[0]) {
              _that.ShowAnimateLine = true
              document.getElementsByClassName(`TabActiveLine-${_that.RefId}`)[0].style.display = 'block'
            }
            _that.CreateActiveAnimate(Math.floor(swiper * _that.AnimateStepCount))
          },
          slideChange: function () {
            _that.TabActiveIndex = this.activeIndex
            _that.$emit('Change', { ..._that.TabArr[_that.TabActiveIndex] })
          },
          transitionEnd: function () {
            _that.ShowAnimateLine = false
            document.getElementsByClassName(`TabActiveLine-${_that.RefId}`)[0].style.display = 'none'
            if (this.activeIndex + 1 === _that.TabArr.length) {
              _that.CreateActiveAnimate(_that.AnimateStepCount - 1)
            }
            _that.CheckActiveVisible()
          }
        }
      }
    }
  },
  created () {
    this.TabOptions.slidesPerView = this.ShowLength
    if (this.ActiveId !== null) {
      this.TabArr.map((item, index) => {
        if (item.id === this.ActiveId) {
          this.TabOptions.initialSlide = index
        }
      })
    }
  },
  computed: {
    SwiperTab () {
      return this.$refs[`SwiperTab-${this.RefId}`].swiper
    },
    SwiperTabContent () {
      return this.$refs[`SwiperTabContent-${this.RefId}`].swiper
    }
  },
  components: {
    swiper,
    swiperSlide
  },
  mounted () {
    this.$nextTick(() => {
      this.Init()
    })
  },
  methods: {
    Init () { // 初始化
      if (this.TabArr.length) {
        this.CreateActiveLine() // 创建选中线框
      }
    },
    CutTab (index) { // 切换tab
      this.SwiperTabContent.slideTo(index, false)
      this.TabActiveIndex = index
    },
    CheckActiveVisible () { // 校验选中tab是否视图可见
      let ParentWidth = document.getElementsByClassName(`SwiperTabContainer-${this.RefId}`)[0].getBoundingClientRect().width
      let SwiperTabItem = document.getElementsByClassName(`SwiperTab-Item-${this.RefId}`)
      let SwiperTabItemWidth = SwiperTabItem[this.TabActiveIndex].getBoundingClientRect().width
      let SwiperTabItemLeft = SwiperTabItem[this.TabActiveIndex].getBoundingClientRect().left
      if (SwiperTabItemLeft < 0 || SwiperTabItemLeft > ParentWidth - SwiperTabItemWidth) {
        this.SwiperTab.slideTo(this.TabActiveIndex, false)
      }
    },
    CreateActiveAnimate (progress) { // 创建动画
      if (progress - 0 >= 0 && progress - 0 < this.AnimateStepCount) {
        let TabActiveLine = document.getElementsByClassName(`TabActiveLine-${this.RefId}`)[0]
        if (TabActiveLine) {
          TabActiveLine.style.width = `${this.AnimateArr[progress].width}px`
          TabActiveLine.style.left = `${this.AnimateArr[progress].left}px`
        }
      }
    },
    CreateAnimateArr (TabActiveLine = null, SwiperTabItem = []) { // 创建动画数据
      TabActiveLine.style.left = `${SwiperTabItem[0].getBoundingClientRect().left}px`
      TabActiveLine.style.width = `${SwiperTabItem[0].getBoundingClientRect().width}px`
      let InitAnimateArr = []
      for (let n = 0; n < SwiperTabItem.length; n++) {
        InitAnimateArr.push({ width: SwiperTabItem[n].getBoundingClientRect().width, left: SwiperTabItem[n].getBoundingClientRect().left })
      }
      InitAnimateArr.map((item, index) => {
        if (index + 1 < InitAnimateArr.length) {
          for (let n = 0; n < this.AnimateStep; n++) {
            this.AnimateArr.push({
              left: item.left + (InitAnimateArr[index + 1].left - item.left) * n / (this.AnimateStep - 1),
              width: item.width + (InitAnimateArr[index + 1].width - item.width) * n / (this.AnimateStep - 1)
            })
          }
        }
      })
    },
    CreateActiveLine () { // 创建选中线框
      window.clearInterval(this.Timer)
      this.Timer = window.setInterval(() => {
        let SwiperTabItem = document.getElementsByClassName(`SwiperTab-Text-${this.RefId}`)
        if (SwiperTabItem) {
          window.clearInterval(this.Timer)
          let TabActiveLineContainer = document.createElement('div')
          TabActiveLineContainer.className = `TabActiveLineContainer-${this.RefId}`
          TabActiveLineContainer.style.position = 'absolute'
          TabActiveLineContainer.style.left = '0'
          TabActiveLineContainer.style.top = '0'
          TabActiveLineContainer.style.bottom = '0'
          TabActiveLineContainer.style.zIndex = '1'
          TabActiveLineContainer.style.width = `${this.TabArr.length * SwiperTabItem[0].getBoundingClientRect().width}px`

          let TabActiveLine = document.createElement('div')
          TabActiveLine.className = `TabActiveLine-${this.RefId}`
          TabActiveLine.style.position = 'absolute'
          TabActiveLine.style.height = '2px'
          TabActiveLine.style.bottom = '0'
          TabActiveLine.style.background = this.ActiveColor
          TabActiveLineContainer.appendChild(TabActiveLine)
          document.getElementsByClassName(`SwiperTab-${this.RefId}`)[0].firstElementChild.appendChild(TabActiveLineContainer)
          this.CreateAnimateArr(TabActiveLine, SwiperTabItem) // 创建动画数据
        }
      }, 30)
    }
  }
}
</script>

<style lang="scss" scoped>
@import "page.scss";
</style>
