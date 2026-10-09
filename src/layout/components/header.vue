<template>
  <el-icon class="ex-icon" @click="expandOrFoldSlider">
    <el-icon-expand v-if="showExpand"></el-icon-expand>
    <el-icon-fold v-else></el-icon-fold>
  </el-icon>
  <div class="header-logo">
    <img :src="setting.logo" alt="" class="logo">
    <div class="title">{{setting.title}}</div>
  </div>
  <div class="crumb" v-if="currentTitle">
    <template v-if="parentTitle">
      <span class="crumb-parent">{{ parentTitle }}</span>
      <span class="crumb-sep">/</span>
    </template>
    <span class="crumb-current">{{ currentTitle }}</span>
  </div>
  <Setting></Setting>
</template>

<script>
  import { defineComponent, computed, inject } from 'vue'
  import { useRoute } from 'vue-router'
  import HeaderMenu from '@/layout/components/menu/index.vue'
  import Setting from '@/layout/components/setting/index.vue'
  import { useAppStore } from '@/store/app'
  import GTags from '@/layout/components/tags/index.vue'
  import { T } from '@/utils/i18n'

  export default defineComponent({
    name: 'LayerHeader',
    created () {
    },
    components: { HeaderMenu, Setting, GTags },
    watch: {},
    setup (props) {
      const appStore = useAppStore()
      const route = useRoute()
      const setting = computed(() => appStore.setting)
      // remote-ops: 좁은 화면에서는 같은 버튼이 메뉴 서랍을 열고 닫는다(넓은 화면은 원래대로 접기)
      const layout = inject('roLayout', null)
      const isMobile = computed(() => !!(layout && layout.isMobile.value))
      const showExpand = computed(() => isMobile.value ? !layout.drawerOpen.value : setting.value.sideIsCollapse)
      const expandOrFoldSlider = () => {
        if (isMobile.value) {
          layout.toggleDrawer()
          return
        }
        appStore.sideCollapse()
      }
      // remote-ops: 현재 위치(메뉴 묶음 / 화면 이름)
      const currentTitle = computed(() => route.meta?.title ? T(route.meta.title) : '')
      const parentTitle = computed(() => {
        const parent = route.matched.length > 1 ? route.matched[0] : null
        const title = parent?.meta?.title ? T(parent.meta.title) : ''
        return title && title !== currentTitle.value ? title : ''
      })
      return {
        setting,
        showExpand,
        expandOrFoldSlider,
        currentTitle,
        parentTitle,
      }
    },

  })
</script>

<style scoped lang="scss">
  .ex-icon {
    height: 100%;
    display: flex;
    align-items: center;
    font-size: 18px;
    color: var(--el-text-color-secondary);
    cursor: pointer;

    &:hover {
      color: var(--el-color-primary);
    }
  }

  .header-logo {
    display: flex;
    height: 100%;
    align-items: center;
    flex: none;

    .title {
      display: block;
      margin-left: 10px;
      font-size: 15px;
      font-weight: 600;
      white-space: nowrap;
    }

    .logo {
      display: block;
      width: 28px;
      height: 28px;
      border-radius: 7px;
    }
  }

  .crumb {
    display: flex;
    align-items: center;
    gap: 8px;
    min-width: 0;
    padding-left: 14px;
    margin-left: 2px;
    border-left: 1px solid var(--el-border-color-light);
    font-size: 13px;
    white-space: nowrap;
    overflow: hidden;

    .crumb-parent,
    .crumb-sep {
      color: var(--el-text-color-secondary);
    }

    .crumb-current {
      color: var(--el-text-color-primary);
      font-weight: 600;
      overflow: hidden;
      text-overflow: ellipsis;
    }
  }

  @media (max-width: 768px) {
    .header-logo .title,
    .crumb {
      display: none;
    }
  }
</style>
<style lang="scss">

</style>
