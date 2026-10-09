<template>
  <el-config-provider :locale="appStore.setting.locale.value">
    <el-container class="app-shell" :class="{'is-mobile': isMobile, 'is-drawer-open': drawerOpen}" :style="{'--sideBarWidth': sideBarWidth}">
      <el-aside :width="leftWidth" class="app-left">
        <g-aside></g-aside>
      </el-aside>
      <div v-if="isMobile && drawerOpen" class="app-scrim" @click="drawerOpen = false"></div>
      <el-container class="app-container ">
        <el-header class="app-header">
          <g-header></g-header>
        </el-header>
        <div class="header-tags">
          <tags></tags>
        </div>

        <el-main class="app-main">
          <h1 v-if="pageTitle" class="page-title">{{ pageTitle }}</h1>
          <router-view v-slot="{ Component }">
            <transition mode="out-in" name="el-fade-in-linear">
              <keep-alive :include="cachedTags">
                <component :is="Component"/>
              </keep-alive>
            </transition>
          </router-view>
        </el-main>
      </el-container>
    </el-container>
  </el-config-provider>
</template>

<script setup>
  import { useAppStore } from '@/store/app'
  import { useTagsStore } from '@/store/tags'
  import { ref, computed, provide, watch, onBeforeUnmount } from 'vue'
  import { useRoute } from 'vue-router'
  import { T } from '@/utils/i18n'
  import Tags from '@/layout/components/tags/index.vue'
  import GAside from '@/layout/components/aside.vue'
  import GHeader from '@/layout/components/header.vue'

  const appStore = useAppStore()
  const tagStore = useTagsStore()
  const route = useRoute()
  const sideBarWidth = computed(() => appStore.setting.locale.sideBarWidth)

  // remote-ops: 좁은 화면(768px 이하)에서는 사이드바를 서랍처럼 열고 닫는다. 넓은 화면의 접기 동작은 그대로다
  const mq = window.matchMedia('(max-width: 768px)')
  const isMobile = ref(mq.matches)
  const drawerOpen = ref(false)
  const applyMq = () => {
    isMobile.value = mq.matches
    if (!mq.matches) drawerOpen.value = false
  }
  mq.addEventListener('change', applyMq)
  onBeforeUnmount(() => mq.removeEventListener('change', applyMq))
  watch(() => route.fullPath, () => { drawerOpen.value = false })
  provide('roLayout', {
    isMobile,
    drawerOpen,
    toggleDrawer: () => { drawerOpen.value = !drawerOpen.value },
  })

  const leftWidth = computed(() => (!isMobile.value && appStore.setting.sideIsCollapse) ? '64px' : 'var(--sideBarWidth)')
  const pageTitle = computed(() => route.meta?.title ? T(route.meta.title) : '')

  const cachedTags = ref([])

  cachedTags.value = tagStore.cached
</script>

<style lang="scss" scoped>
.app-shell {
  min-height: 100vh;
  background: var(--el-bg-color-page);
}

.app-header {
  background-color: var(--el-bg-color);
  color: var(--el-text-color-primary);
  border-bottom: 1px solid var(--el-border-color-light);
  display: flex;
  align-items: center;
  gap: 12px;
  height: 52px;
  padding: 0 16px;
}

.header-tags {
  height: auto;
  display: flex;
  gap: 6px;
  padding: 8px 16px;
  background-color: var(--el-bg-color);
  border-bottom: 1px solid var(--el-border-color-light);
  overflow-x: auto;
  scrollbar-width: thin;
}

.app-left {
  transition: width 0.3s;
  background-color: var(--el-bg-color);
  border-right: 1px solid var(--el-border-color-light);
  overflow: hidden;
}

.app-container {
  min-height: 100vh;
  min-width: 0;
}

.app-main {
  padding: 20px 24px 32px;
  background: var(--el-bg-color-page);
}

.page-title {
  margin: 0 0 16px;
  font-size: 22px;
  line-height: 1.3;
  font-weight: 600;
  letter-spacing: -0.01em;
  color: var(--el-text-color-primary);
}

.app-scrim {
  position: fixed;
  inset: 0;
  z-index: 2000;
  background: var(--ro-scrim);
}

.app-shell.is-mobile {
  .app-left {
    position: fixed;
    z-index: 2001;
    top: 0;
    bottom: 0;
    left: 0;
    transform: translateX(-100%);
    transition: transform 0.25s;
    box-shadow: var(--el-box-shadow);
  }

  &.is-drawer-open .app-left {
    transform: none;
  }

  .app-main {
    padding: 14px 12px 24px;
  }

  .page-title {
    font-size: 19px;
    margin-bottom: 12px;
  }
}

@media (prefers-reduced-motion: reduce) {
  .app-left,
  .app-shell.is-mobile .app-left {
    transition: none;
  }
}
</style>
