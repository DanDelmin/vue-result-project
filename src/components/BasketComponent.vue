<script setup>
import { computed, reactive, watch } from 'vue'

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

const subtotal = (item) => item.price * item.quantity

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
          <td>
            <div class="basket-item">
              <div class="basket-item__image">
                <img :src="item.imageUrl" :alt="item.name" />
              </div>
              <div class="basket-item__info">
                <h2 class="basket-item__info-h2">{{ item.name }}</h2>
                <p class="basket-item__info-p">{{ item.color }}</p>
                <p class="basket-item__info-p">{{ item.size }}</p>
              </div>
            </div>
          </td>
          <td>
            <p class="basket-item__price">${{ item.price }}</p>
          </td>
          <td>
            <div class="basket-item__quantity">
              <button class="quantity-button" @click="decreaseItemQuantity(item)">–</button>
              <input type="number" :value="item.quantity" min="1" />
              <button class="quantity-button" @click="increaseItemQuantity(item)">+</button>
            </div>
          </td>
          <td>
            <p class="basket-item__price">${{ subtotal(item) }}</p>
          </td>
          <td>
            <button class="btn btn-delete" aria-label="Удалить" @click="removeItem(index)">
              <svg
                class="w-6 h-6 text-gray-800 dark:text-white"
                aria-hidden="true"
                xmlns="http://www.w3.org/2000/svg"
                width="24"
                height="24"
                fill="none"
                viewBox="0 0 24 24"
              >
                <path
                  stroke="currentColor"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M5 7h14m-9 3v8m4-8v8M10 3h4a1 1 0 0 1 1 1v3H9V4a1 1 0 0 1 1-1ZM6 7h12v13a1 1 0 0 1-1 1H7a1 1 0 0 1-1-1V7Z"
                />
              </svg>
            </button>
          </td>
        </tr>

        <tr v-if="!basketItems.length">
          <td colspan="5">
            <p class="basket-table__empty">No items</p>
          </td>
        </tr>

        <tr v-if="basketItems.length">
          <td colspan="5">
            <div class="basket-table__summary">
              <p class="basket-table__total">
                Total <b>${{ totalPrice.toFixed(2) }}</b>
              </p>
              <p>Tax ${{ totalTax.toFixed(2) }}</p>
            </div>
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

.basket-item {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 2rem;
}

.basket-item__image {
  width: 105px;
  height: 120px;
  object-fit: cover;
  border-radius: 12px;
}

.basket-item__info {
  flex-grow: 1;
}

.basket-item__info-h2 {
  font-size: 1.2rem;
  font-weight: 500;
  margin-bottom: 0.5rem;
}

.basket-item__info-p {
  color: #807d7e;
  font-size: 1rem;
  margin: 0 0 0.5rem;
}

.basket-item__price {
  font-size: 1.2rem;
  font-weight: 500;
  min-width: 140px;
}

.btn-delete {
  background-color: transparent;
  color: #8a33fd;
  border: none;
  cursor: pointer;
}

.btn-delete:hover {
  color: #5b1f9d;
}

.basket-item__quantity {
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #f6f6f6;
  border-radius: 12px;
  overflow: hidden;
}

.quantity-button {
  background-color: #f6f6f6;
  color: #3c4242;
  border: none;
  cursor: pointer;
  font-size: 1.2rem;
  width: 30px;
  height: 35px;
  text-align: center;
}

.quantity-button:hover {
  font-weight: 700;
}

.basket-item__quantity input[type='number'] {
  -moz-appearance: textfield;
  -webkit-appearance: none;
  appearance: none;
  width: 30px;
  text-align: center;
  border: none;
  background-color: #f6f6f6;
  color: #3c4242;
  height: 35px;
  line-height: 35px;
  font-size: 1.2rem;
}

/* Chrome, Safari, Edge, Opera */
.basket-item__quantity input[type='number']::-webkit-outer-spin-button,
.basket-item__quantity input[type='number']::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

.basket-table__empty {
  text-align: center;
  color: #a7a7a7;
}

.basket-table__summary {
  text-align: right;
}

.basket-table__total {
  font-size: 1.4rem;
}
</style>
