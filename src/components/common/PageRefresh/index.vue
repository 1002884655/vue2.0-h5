<template>
  <div class="components PageRefresh">
    <scroller height="100%" :on-refresh="Refresh" :on-infinite="Infinite" :ref="RefId" :refreshText="RefreshText" :noDataText="NoDataText" :refreshLayerColor="RefreshTextColor" :loadingLayerColor="LoadingTextColor">
      <slot></slot>
    </scroller>
  </div>
</template>

<script>
export default {
  name: 'PageRefresh',
  props: {
    LoadingTextColor: { // 上拉加载文本颜色
      default: `#999`,
      type: String
    },
    RefreshTextColor: { // 下拉刷新文本颜色
      default: `#999`,
      type: String
    },
    NoDataText: {
      default: `没有更多数据`,
      type: String
    },
    RefreshText: {
      default: `正在请求数据`,
      type: String
    },
    RefId: {
      default: `PageRefresh-${Date.now()}`,
      type: String
    }
  },
  data: () => {
    return {
      PageRefresh: null
    }
  },
  mounted () {
    this.$nextTick(() => {
      this.Init()
    })
  },
  methods: {
    Init () {
    },
    Refresh (done) {
      this.$emit('Refresh', done)
    },
    Infinite (done) {
      this.$emit('Infinite', done)
    },
    ScrollTo (top = 0) {
      this.$refs[this.RefId].scrollTo(top)
    }
  }
}
</script>

<style lang="scss" scoped>
@import "page.scss";
</style>
