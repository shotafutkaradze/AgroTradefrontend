<script setup>
import StatusBadge from './StatusBadge.vue'

defineProps({
  orders: {
    type: Array,
    default: () => []
  },
  labels: {
    type: Object,
    required: true
  },
  showDate: Boolean,
  emptyText: {
    type: String,
    default: ''
  },
  formatDate: {
    type: Function,
    default: null
  }
})

const emit = defineEmits(['open'])
</script>

<template>
  <div class="order-table admin-wide">
    <div class="table-head">
      <span>{{ labels.order }}</span>
      <span>{{ showDate ? labels.date : labels.customer }}</span>
      <span>{{ labels.amount }}</span>
      <span>{{ labels.payment }}</span>
      <span>{{ labels.bankStatus }}</span>
      <span>{{ labels.status }}</span>
    </div>
    <button
      v-for="order in orders"
      :key="order.id || order.orderNumber"
      class="table-row admin-order-row"
      @click="emit('open', order)"
    >
      <strong>{{ order.orderNumber }}</strong>
      <span>{{ showDate && formatDate ? formatDate(order.createdAt) : order.customerName }}</span>
      <strong>{{ Number(order.total).toLocaleString() }} ₾</strong>
      <span>{{ order.paymentLabel }}</span>
      <StatusBadge tone="payment" :label="order.paymentStatusLabel" />
      <StatusBadge :label="order.status" />
    </button>
  </div>
  <p v-if="!orders.length && emptyText" class="admin-empty-text">{{ emptyText }}</p>
</template>
