<template>
  <div class="Page">
    <MainPage :HideMainTab="false">
      <SwiperTab :TabArr="[{ name: '选项一一', id: 1 }, { name: '选项二', id: 2 }, { name: '选项三三', id: 3 }, { name: '选项四', id: 4 }]" @Change="TabChange">
        <div class="TabContent" slot="TabSwiperSlot-1">
          <PageRefresh @Refresh="Refresh" @Infinite="Infinite"></PageRefresh>
        </div>
        <div class="TabContent" slot="TabSwiperSlot-2">
          <PageRefresh @Refresh="Refresh" @Infinite="Infinite"></PageRefresh>
        </div>
        <div class="TabContent" slot="TabSwiperSlot-3">
          <PageRefresh @Refresh="Refresh" @Infinite="Infinite"></PageRefresh>
        </div>
        <div class="TabContent" slot="TabSwiperSlot-4">
          <PageRefresh @Refresh="Refresh" @Infinite="Infinite"></PageRefresh>
        </div>
      </SwiperTab>
    </MainPage>
  </div>
</template>

<script>
import { createNamespacedHelpers } from 'vuex'
const { mapState: mapUserState, mapActions: mapUserActions } = createNamespacedHelpers('user')
const MainPage = () => import('@/components/common/MainPage')
const PageRefresh = () => import('@/components/common/PageRefresh')
const SwiperTab = () => import('@/components/common/SwiperTab')
export default {
  name: 'index',
  components: {
    MainPage,
    PageRefresh,
    SwiperTab
  },
  data: () => {
    return {
    }
  },
  computed: {
    ...mapUserState({
      UserInfo: x => x.UserInfo
    })
  },
  created () {
    // this.GetUserInfoByToken({ params: { token: `6040529af14c2e325cd57cfd` } }).then(() => { })
  },
  methods: {
    ...mapUserActions([
      'GetUserInfoByToken'
    ]),
    TabChange () {
      console.log(111)
    },
    Refresh (done) {
      console.log('Refresh')
      window.setTimeout(() => {
        done(true)
      }, 1000)
    },
    Infinite (done) {
      console.log('Infinite')
      window.setTimeout(() => {
        done(true)
      }, 1000)
    }
  }
}
</script>

<style lang="scss" scoped>
@import "page.scss";
</style>
