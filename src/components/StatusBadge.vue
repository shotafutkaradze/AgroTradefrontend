<script setup>
defineProps({
  label: {
    type: String,
    required: true
  },
  tone: {
    type: String,
    default: 'order'
  }
})
</script>

<template>
  <em class="status-badge" :class="`status-badge-${tone}`">{{ label }}</em>
</template>
