<script lang="ts" setup>
const props = withDefaults(
  defineProps<{ checked: boolean; disabled?: boolean; size?: 'default' | 'small' | 'xsmall'; placement: 'left' | 'right' }>(),
  {
    size: 'small',
    placement: 'left',
  },
)

const emit = defineEmits(['change', 'update:checked'])

const checked = useVModel(props, 'checked', emit)

const switchSize = computed(() => (['default', 'small'].includes(props.size) ? props.size : undefined))

const onChange = (e: boolean, updateValue = false) => {
  if (updateValue) {
    checked.value = e
  }

  emit('change', e)
}
</script>

<template>
  <span v-if="placement === 'right' && $slots.default" class="cursor-pointer pr-2" @click="onChange(!checked, true)">
    <slot />
  </span>
  <a-switch
    v-model:checked="checked"
    :disabled="disabled"
    class="nc-switch"
    :class="{
      'size-xsmall': size === 'xsmall',
    }"
    v-bind="$attrs"
    :size="switchSize"
    @change="onChange"
  >
  </a-switch>
  <span v-if="placement === 'left' && $slots.default" class="cursor-pointer pl-2" @click="onChange(!checked, true)">
    <slot />
  </span>
</template>

<style lang="scss" scoped>
.size-xsmall {
  @apply h-3.5 min-w-[26px] leading-[14px];

  :deep(.ant-switch-handle) {
    @apply h-[10px] w-[10px] top-[2px] left-[calc(100%_-_24px)];
  }

  :deep(.ant-switch-inner) {
    @apply !mr-[5px] !ml-[18px] !my-0;
  }

  &.ant-switch-checked {
    :deep(.ant-switch-handle) {
      @apply left-[calc(100%_-_12px)];
    }

    :deep(.ant-switch-inner) {
      @apply !mr-[18px] !ml-[5px];
    }
  }
}
</style>
