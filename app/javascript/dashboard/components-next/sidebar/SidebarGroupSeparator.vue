<script setup>
import Icon from 'next/icon/Icon.vue';

defineProps({
  label: {
    type: String,
    default: '',
  },
  icon: {
    type: String,
    default: '',
  },
  collapsible: {
    type: Boolean,
    default: false,
  },
  collapsed: {
    type: Boolean,
    default: false,
  },
});

const emit = defineEmits(['toggle']);
</script>

<template>
  <div
    :class="[
      'flex items-center gap-2 px-2 py-1.5 rounded-lg h-8 text-n-slate-10 select-none transition-colors',
      collapsible ? 'cursor-pointer hover:bg-n-alpha-2 hover:text-n-slate-12' : 'pointer-events-none',
    ]"
    @click="collapsible && emit('toggle')"
  >
    <Icon v-if="icon" :icon="icon" class="size-4" />
    <span class="text-sm font-medium leading-5 flex-grow">
      {{ label }}
    </span>
    <button
      v-if="collapsible"
      type="button"
      class="flex items-center justify-center size-5 rounded hover:bg-n-alpha-2 text-n-slate-11 transition-all"
      :title="collapsed ? 'Expandir' : 'Recolher'"
      @click.stop="emit('toggle')"
    >
      <svg v-if="collapsed" xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
        <line x1="12" y1="5" x2="12" y2="19"></line>
        <line x1="5" y1="12" x2="19" y2="12"></line>
      </svg>
      <svg v-else xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
        <line x1="5" y1="12" x2="19" y2="12"></line>
      </svg>
    </button>
  </div>
</template>
