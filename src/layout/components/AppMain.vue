<template>
  <section class="app-main">
    <div :style="leftStyle" style="height: 100%; overflow: auto">
      <!--<transition name="fade-transform" mode="out-in">-->
      <keep-alive :include="cachedViews">
        <router-view :key="key" />
      </keep-alive>
      <!--</transition>-->
    </div>
    <!--todo 先写死-->
    <div v-if="showMobile" style="width: 350px;height: calc(100% - 84px);position: absolute;top: 84px;right: 10px;overflow: auto;">
      <android-stf-controller v-if="screenType === 'stf'" />
      <android-scrcpy-controller v-else-if="screenType === 'scrcpy'" />
      <ios-controller v-else-if="screenType === 'ios'" />
    </div>
    <div v-if="showBrowser" style="width: 100px;height: calc(100% - 84px);position: absolute;top: 84px;right: 10px;overflow: auto;">
      <browser-websocket-board />
    </div>
  </section>
</template>

<script>
import AndroidStfController from '@/pages/mobile/android/components/AndroidStfController'
import AndroidScrcpyController from '@/pages/mobile/android/components/AndroidScrcpyController'
import IosController from '@/pages/mobile/ios/components/IosController'
import BrowserWebsocketBoard from '@/pages/browser/BrowserWebsocketBoard'

export default {
  name: 'AppMain',
  components: {
    AndroidStfController,
    AndroidScrcpyController,
    IosController,
    BrowserWebsocketBoard
  },
  computed: {
    leftStyle() {
      // todo 先写死
      if (this.$store.state.mobile.show) {
        return 'width: calc(100% - 360px)'
      } else if (this.$store.state.browser.show) {
        return 'width: calc(100% - 110px)'
      } else {
        return ''
      }
    },
    showMobile() {
      return this.$store.state.mobile.show
    },
    showBrowser() {
      return this.$store.state.browser.show
    },
    screenType() {
      const platform = this.$store.state.mobile.platform
      const systemVersion = this.$store.state.mobile.systemVersion
      if (platform === 1) {
        if (parseInt(systemVersion) >= 5) {
          // 版本5以上 scrcpy
          return 'scrcpy'
        } else {
          return 'stf'
        }
      } else if (platform === 2) { // ios
        return 'ios'
      } else {
        return 'unknow platform'
      }
    },
    routes() {
      return this.$store.getters.permission_routes
    },
    platform() {
      return this.$store.state.project.platform
    },
    cachedViews() {
      return this.$store.state.tagsView.cachedViews
    },
    key() {
      return this.$route.fullPath
    }
  },
  created() {
    // 移除与项目冲突的路由
    const toBeRemovedRoutePaths = this.platform === 1 ? ['/browser'] : this.platform === 2 ? ['/browser', '/driver'] : ['/app', '/mobile', '/driver']
    for (let i = 0; i < this.routes.length; i++) {
      if (toBeRemovedRoutePaths.includes(this.routes[i].path)) {
        this.routes.splice(i, 1)
        i = i - 1
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.app-main {
  height: 100%;
  width: 100%;
  position: relative;
  overflow: hidden;
}

.fixed-header+.app-main {
  padding-top: 50px;
}

.hasTagsView {
  .app-main {
    /* 84 = navbar + tags-view = 50 + 34 */
    min-height: calc(100vh - 84px);
  }

  .fixed-header+.app-main {
    padding-top: 84px;
  }
}
</style>

<style lang="scss">
// fix css style bug in open el-dialog
.el-popup-parent--hidden {
  .fixed-header {
    padding-right: 15px;
  }
}
</style>
