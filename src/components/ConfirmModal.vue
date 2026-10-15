<script setup>
import { X } from 'lucide-vue-next'

defineProps({
  open: Boolean,
  eyebrow: { type: String, default: 'დადასტურება' },
  title: { type: String, required: true },
  description: { type: String, required: true },
  cancelText: { type: String, default: 'არა' },
  confirmText: { type: String, required: true },
  loading: Boolean
})

const emit = defineEmits(['cancel', 'confirm'])
</script>

<template>
  <transition name="modal-fade">
    <div v-if="open" class="admin-product-modal" @click.self="emit('cancel')">
      <section class="admin-confirm-modal">
        <div class="admin-product-form-head">
          <div>
            <small>{{ eyebrow }}</small>
            <h2>{{ title }}</h2>
            <p class="confirm-description">{{ description }}</p>
          </div>
          <button type="button" :disabled="loading" @click="emit('cancel')"><X :size="18" /></button>
        </div>
        <div class="admin-product-form-actions">
          <button type="button" :disabled="loading" @click="emit('cancel')">{{ cancelText }}</button>
          <button type="button" :disabled="loading" @click="emit('confirm')">{{ confirmText }}</button>
        </div>
      </section>
    </div>
  </transition>
</template>
