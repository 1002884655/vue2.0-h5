<template>
  <div class="components MainPage flex-v">
    <div class="Top"></div>
    <div class="Content flex-item">
      <slot></slot>
    </div>
    <div class="Bottom flex-h" v-if="!HideMainTab">
      <div class="flex-item MainBottomTabItem" v-for="(item, index) in MainTabArr" :key="index">
        <router-link :to="{ name: item.router }">
          <i class="iconfont" v-if="item.icon !== undefined" :class="[MainTabActiveId === item.id ? item.activeIcon : item.icon]" :style="{color: MainTabActiveId === item.id ? MainTabActiveColor : MainTabDefaultColor}"></i><br v-if="item.icon !== undefined">
          <img v-if="item.img !== undefined" :src="[MainTabActiveId === item.id ? item.activeImg : item.img]" alt=""><br v-if="item.img !== undefined">
          <span :style="{color: MainTabActiveId === item.id ? MainTabActiveColor : MainTabDefaultColor}">{{item.name}}</span>
        </router-link>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState, mapMutations } from 'vuex'
export default {
  name: 'MainPage',
  props: {
  },
  data () {
    return {
    }
  },
  computed: {
    ...mapState({
      MainTabDefaultColor: x => x.MainTabDefaultColor, // 页底tab默认色值
      MainTabActiveColor: x => x.MainTabActiveColor, // 页底tab选中色值
      HideMainTab: x => x.HideMainTab, // 隐藏页底tab
      MainTabActiveId: x => x.MainTabActiveId, // 页底tab选中id
      MainTabArr: x => x.MainTabArr // 页底tab数据
    })
  },
  created () {
  },
  mounted () {
    this.$nextTick(() => {
    })
  },
  methods: {
    ...mapMutations([
      'EditHideMainTab',
      'EditMainTabActiveId'
    ])
  }
}
</script>

<style lang="scss" scoped>
@import "page.scss";
</style>
