<template>
  <el-tag v-for="(t, i) in tags"
          :key="t.name"
          class="tag"
          :closable="t.closeable"
          @close="close(t)"
          @click="toTag(t)"
          :type="t.active?'primary':'info'"
          :effect="t.active?'dark':'plain'">
    {{ T(t.title) }}
  </el-tag>
</template>

<script>
  import { defineComponent, ref, onMounted, watch } from 'vue'
  import { useTagsStore } from '@/store/tags'
  import { useRoute, useRouter } from 'vue-router'
  import { T } from '@/utils/i18n'

  export default defineComponent({
    name: 'Index',
    setup () {
      const tags = ref([])
      const tagsStore = useTagsStore()
      const route = useRoute()
      const router = useRouter()
      tags.value = tagsStore.tags

      const addTag = (route) => {
        if (!route.meta?.hide && route.name) {
          tagsStore.addTag(route)
        }
      }
      const close = (tag) => {
        tagsStore.removeTag(tag)
        if (tag.active) {
          toLastTag()
        }
      }
      const toLastTag = () => {
        if (tags.value.length) {
          router.push({ name: tags.value[tags.value.length - 1].name })
        }
      }
      const init = () => {
        if (!tagsStore.tags.length) {
          tagsStore.initTags()
        }
        addTag(route)
      }

      const toTag = (tag) => {
        if (tag.name !== route.name) {
          router.push({ name: tag.name })
        }
      }

      onMounted(init)
      watch(route, (val) => {
        addTag(val)
      })
      return {
        tags,
        addTag,
        close,
        toLastTag,
        toTag,
        T,
      }
    },
  })
</script>

<style lang="scss" scoped>

.tag {
  // remote-ops: 둥근 칩. 현재 탭은 옅은 강조색, 나머지는 테두리만
  flex: none;
  height: 26px;
  padding: 0 10px;
  border-radius: 999px;
  cursor: pointer;

  &.el-tag--dark.el-tag--primary {
    --el-tag-bg-color: var(--el-color-primary-light-9);
    --el-tag-border-color: transparent;
    --el-tag-text-color: var(--el-color-primary);
    font-weight: 600;
  }

  &.el-tag--plain.el-tag--info {
    --el-tag-bg-color: transparent;
    --el-tag-border-color: var(--el-border-color);
    --el-tag-text-color: var(--el-text-color-secondary);
  }

  &.active {
  }
}
</style>
