<template>
  <el-menu
          class="menus"
          :collapse="isCollapse"
          :default-active="activeIndex"
          router
  >
    <menu-item v-for="(route,index) in routes" :key="route.name" :route="route"></menu-item>
  </el-menu>
</template>

<script>
  import { defineComponent, ref, onMounted, watch, computed, inject } from 'vue'
  import { useRouteStore } from '@/store/router'
  import MenuItem from '@/layout/components/menu/item.vue'
  import { useRoute } from 'vue-router'
  import { useAppStore } from '@/store/app'

  export default defineComponent({
    name: 'Menu',
    created () {
    },
    components: { MenuItem },
    setup () {
      const routes = ref([])
      const route = useRoute()
      const app = useAppStore()
      // remote-ops: 휴대폰 서랍 메뉴는 접지 않고 펼친 채로 보여 준다
      const layout = inject('roLayout', null)
      const isCollapse = computed(() => !(layout && layout.isMobile.value) && app.setting.sideIsCollapse)
      const activeIndex = computed(() => route.name)

      routes.value = useRouteStore().routes
      return {
        routes,
        activeIndex,
        isCollapse,
      }
    },

  })
</script>

<style lang="scss" scoped>
  .menus {
    // remote-ops: 밝은 사이드바(색은 테마 변수로)
    --el-menu-bg-color: var(--el-bg-color);
    --el-menu-text-color: var(--el-text-color-regular);
    --el-menu-hover-text-color: var(--el-text-color-primary);
    --el-menu-hover-bg-color: var(--el-fill-color-light);
    --el-menu-active-color: var(--el-color-primary);
    --el-menu-item-height: 40px;
    --el-menu-sub-item-height: 38px;
    --el-menu-base-level-padding: 16px;
    --el-menu-level-padding: 14px;
    min-height: 100vh;
    padding: 10px 0 24px;
    border-right: none;
    &:not(.el-menu--collapse) {
      width: var(--sideBarWidth);
    }

    :deep(.el-menu-item),
    :deep(.el-sub-menu__title) {
      margin: 1px 8px;
      border-radius: 6px;
    }

    :deep(.el-menu-item.is-active) {
      background-color: var(--el-color-primary-light-9);
      font-weight: 600;
    }

    // 묶음 제목(내 항목 · 시스템)은 작고 옅게
    :deep(.el-sub-menu__title) {
      height: 36px;
      font-size: 12px;
      font-weight: 600;
      color: var(--el-text-color-secondary);
    }

    &.el-menu--collapse :deep(.el-sub-menu__title) {
      height: var(--el-menu-item-height);
    }
  }
</style>
<style>
</style>
