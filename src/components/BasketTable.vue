<script setup>
import { computed, reactive, watch } from 'vue'
import BasketTableItem from './BasketTableItem.vue'
import BasketTableSummary from './BasketTableSummary.vue'

const STORAGE_KEY = 'cart:v1'
const DEFAULT_ITEMS = [
  {
    id: 1,
    name: 'Blue Flower Print Crop Top',
    color: 'Yellow',
    size: 'M',
    price: 29.0,
    quantity: 1,
    imageUrl: './assets/crop-top.png',
  },
  {
    id: 2,
    name: 'Levender Hoodie',
    color: 'Levender',
    size: 'XXL',
    price: 119.0,
    quantity: 1,
    imageUrl: './assets/hoodie.png',
  },
  {
    id: 3,
    name: 'Black Sweatshirt',
    color: 'Black',
    size: 'XXL',
    price: 129.0,
    quantity: 1,
    imageUrl: './assets/sweatshirt.png',
  },
]

const loadCart = (STORAGE_KEY) => {
  try {
    const savedCartString = localStorage.getItem(STORAGE_KEY)
    if (!savedCartString) return null
    return JSON.parse(savedCartString)
  } catch (error) {
    console.error('Ошибка при загрузке корзины:', error)
    return null
  }
}

const savedCart = loadCart(STORAGE_KEY)

const basketItems = reactive(savedCart || DEFAULT_ITEMS)

watch(
  basketItems,
  (updatedCart) => {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(updatedCart))
  },
  { deep: true },
)

const increaseItemQuantity = (item) => {
  const next = Math.min(item.quantity + 1, 99)
  item.quantity = next
}

const decreaseItemQuantity = (item) => {
  const prev = Math.max(item.quantity - 1, 1)
  item.quantity = prev
}

const removeItem = (index) => {
  basketItems.splice(index, 1)
}

const totalPrice = computed(() =>
  basketItems.reduce((sum, item) => sum + item.price * item.quantity, 0),
)

const totalTax = computed(() => {
  return (totalPrice.value * 10) / 100
})
</script>

<template>
  <div class="container basket">
    <table class="basket-table">
      <thead class="basket-table__header">
        <tr>
          <th>Product Details</th>
          <th>Price</th>
          <th>Quantity</th>
          <th>Subtotal</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody class="basket-table__body">
        <tr v-for="(item, index) in basketItems" :key="item.id">
          <BasketTableItem
            :item="item"
            @increase-item-quantity="increaseItemQuantity"
            @decrease-item-quantity="decreaseItemQuantity"
            @remove-item="removeItem"
            :key="index"
          />
        </tr>

        <tr v-if="!basketItems.length">
          <td colspan="5">
            <p class="basket-table__empty">No items</p>
          </td>
        </tr>

        <tr v-if="basketItems.length">
          <td colspan="5">
            <BasketTableSummary :total-tax="totalTax" :total-price="totalPrice" />
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
body {
  background-color: #f0f0f0;
  font-family:
    system-ui,
    -apple-system,
    BlinkMacSystemFont,
    'Segoe UI',
    Roboto,
    Oxygen,
    Ubuntu,
    Cantarell,
    'Open Sans',
    'Helvetica Neue',
    sans-serif,
    sans-serif;
}

.container {
  max-width: 1200px;
  margin: 100px auto;
  background-color: #fff;
}

.basket-table {
  width: 100%;
  border-collapse: collapse;
}

.basket-table__header {
  background-color: #3c4242;
  color: #fff;
  font-weight: 400;
  text-transform: uppercase;
}

.basket-table__empty {
  text-align: center;
  color: #a7a7a7;
}
</style>
<style>
.basket-table__body td {
  padding: 3rem 2rem;
  text-align: center;
}

.basket-table__body td:first-child {
  text-align: left;
  padding-left: 5rem;
}

.basket-table__body td:last-child {
  padding-right: 5rem;
}

.basket-table__header th {
  padding: 2rem 1rem;
  font-weight: 400;
  text-align: center;
  border: 0;
}

.basket-table__header th:first-child {
  text-align: left;
  padding-left: 5rem;
}

.basket-table__header th:last-child {
  padding-right: 5rem;
}
</style>
