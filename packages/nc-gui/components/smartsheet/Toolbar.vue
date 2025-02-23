<script lang="ts" setup>
const { isGrid, isGallery, isKanban, isMap, isCalendar } = useSmartsheetStoreOrThrow()

const isPublic = inject(IsPublicInj, ref(false))

const { isMobileMode } = useGlobal()
const { isLeftSidebarOpen } = storeToRefs(useSidebarStore())

const { isViewsLoading } = storeToRefs(useViewsStore())

const containerRef = ref<HTMLElement>()

const { width } = useElementSize(containerRef)

const isTab = computed(() => {
  if (!isCalendar.value) return false
  return width.value > 1200
})

const { allowCSVDownload } = useSharedView()
</script>

<template>
  <div
    v-if="!isMobileMode || isCalendar"
    ref="containerRef"
    :class="{
      'px-4': isMobileMode,
    }"
    class="nc-table-toolbar relative px-3 flex gap-2 items-center border-b border-gray-200 overflow-hidden xs:(min-h-14) min-h-9 max-h-9 z-7"
  >
    <template v-if="isViewsLoading">
      <a-skeleton-input :active="true" class="!w-44 !h-4 ml-2 !rounded overflow-hidden" />
    </template>
    <template v-else>
      <div
        :class="{
          'min-w-34/100': !isMobileMode && isLeftSidebarOpen && isCalendar,
          'min-w-39/100': !isMobileMode && !isLeftSidebarOpen && isCalendar,
          'gap-1': isCalendar,
        }"
        class="flex items-center gap-3"
      >
        <LazySmartsheetToolbarMappedBy v-if="isMap" />
        <LazySmartsheetToolbarCalendarHeader v-if="isCalendar" />
        <LazySmartsheetToolbarCalendarToday v-if="isCalendar" />

        <LazySmartsheetToolbarCalendarRange v-if="isCalendar" />

        <LazySmartsheetToolbarFieldsMenu v-if="isGrid || isGallery || isKanban || isMap" :show-system-fields="false" />

        <LazySmartsheetToolbarStackedBy v-if="isKanban" />

        <LazySmartsheetToolbarColumnFilterMenu v-if="isGrid || isGallery || isKanban || isMap" />

        <LazySmartsheetToolbarGroupByMenu v-if="isGrid" />

        <LazySmartsheetToolbarSortListMenu v-if="isGrid || isGallery || isKanban" />
      </div>

      <LazySmartsheetToolbarCalendarMode v-if="isCalendar && isTab" :tab="isTab" />

      <template v-if="!isMobileMode">
        <LazySmartsheetToolbarRowHeight v-if="isGrid" />

        <!-- <LazySmartsheetToolbarQrScannerButton v-if="isMobileMode && (isGrid || isKanban || isGallery)" /> -->

        <LazySmartsheetToolbarExport v-if="isPublic && allowCSVDownload" />

        <div class="flex-1" />
      </template>

      <LazySmartsheetToolbarCalendarActiveView v-if="isCalendar" />

      <LazySmartsheetToolbarSearchData
        v-if="isGrid || isGallery || isKanban"
        :class="{
          'shrink': !isMobileMode,
          'w-full': isMobileMode,
        }"
      />
      <div v-if="isCalendar && isMobileMode" class="flex-1 pointer-events-none" />

      <LazySmartsheetToolbarCalendarMode v-if="isCalendar && !isTab" :tab="isTab" />

      <LazySmartsheetToolbarFieldsMenu v-if="isCalendar && !isMobileMode" :show-system-fields="false" />
      <LazySmartsheetToolbarColumnFilterMenu v-if="isCalendar && !isMobileMode" />
    </template>
  </div>
</template>

<style scoped>
.nc-table-toolbar-mobile {
  @apply flex-wrap h-auto py-2;
}
</style>
