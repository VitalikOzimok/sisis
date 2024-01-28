<script setup>
import { ref, watch, provide, computed } from 'vue'
import axios from 'axios'

import One from './components/One.vue'
import Drawer from './components/Drawer.vue'

const cart = ref([])

const drawerOpen = ref(false)

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0))
const vatPrice = computed(() => Math.round((totalPrice.value * 5) / 100))

const closeDrawer = () => {
  drawerOpen.value = false
}

const openDrawer = () => {
  drawerOpen.value = true
}

const addToCart = (item) => {
  cart.value.push(item)
  item.isAdded = true
}
const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1)
  item.isAdded = false
}

watch(
  cart,
  () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  { deep: true }
)

provide('cart', {
  cart,
  closeDrawer,
  openDrawer,
  addToCart,
  removeFromCart
})
</script>

<template>
  <div>
    <Drawer v-if="drawerOpen" :total-price="totalPrice" :vatPrice="vatPrice" />

    <div
      class="bg-gradient-to-r from-cyan-100 to-blue-300 w-4/5 m-auto h-screen border border-red-500 rounded-xl shadow-xl mt-14"
    >
      <One :total-price="totalPrice" @open-drawer="openDrawer" />
      <RouterView />

      <div class="p-10"></div>
    </div>
  </div>
</template>
