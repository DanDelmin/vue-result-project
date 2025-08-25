<script setup>
import { defineProps } from 'vue'

const props = defineProps({
  totalPrice: {
    type: Number,
    required: true,
  },
  totalTax: {
    type: Number,
    required: true,
  },
})
</script>
<template>
  <div class="basket-table__summary">
    <p class="basket-table__total">
      Total <b>${{ props.totalPrice.toFixed(2) }}</b>
    </p>
    <p>Tax ${{ props.totalTax.toFixed(2) }}</p>
  </div>
</template>
<style scoped>
.basket-table__summary {
  text-align: right;
}

.basket-table__total {
  font-size: 1.4rem;
}
</style>
