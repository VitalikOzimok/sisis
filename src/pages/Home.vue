<script setup>
import { reactive, watch, ref, onMounted } from 'vue'
import Cardlist from '../components/Cardlist.vue'
import axios from 'axios'
import { inject } from 'vue'
import debounce from 'lodash.debounce'

const items = ref([])

const { cart, addToCart, removeFromCart } = inject('cart')

const filters = reactive({
  sortBy: 'title',
  searchBy: ''
})

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
}

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

const onSearchInput = debounce((event) => {
  filters.searchBy = event.target.value
}, 150)

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        parentId: item.id,
        item
      }
      item.isFvorite = true
      const { data } = await axios.post('https://2962b1b061b4abcd.mokky.dev/favorites', obj)
      item.favoriteId = data.id
    } else {
      item.isFvorite = false
      await axios.delete(`https://2962b1b061b4abcd.mokky.dev/favorites/${item.favoriteId}`)
      item.favoriteId = null
    }
    console.log()
  } catch (err) {
    console.log(err)
  }
}
const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get('https://2962b1b061b4abcd.mokky.dev/favorites')

    items.value = items.value.map((item) => {
      const favorite = favorites.find((favorite) => favorite.parentId === item.id)

      if (!favorite) {
        return item
      }
      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id
      }
    })
  } catch (err) {
    console.log(err)
  }
}

const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy
    }
    if (filters.searchBy) {
      params.title = `*${filters.searchBy}*`
    }

    const { data } = await axios.get('https://2962b1b061b4abcd.mokky.dev/items', {
      params
    })
    items.value = data.map((obj) => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }))
  } catch (err) {
    console.log(err)
  }
}
onMounted(async () => {
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []

  await fetchItems()
  await fetchFavorites()

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
  }))
})
watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})

watch(filters, fetchItems)
</script>

<template>
  <div>
    <div class="flex justify-between items-center">
      <h2 class="text-3xl font-bold mb-8">Все кросовки</h2>

      <div class="flex gap-4">
        <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
          <option value="name">По названию</option>
          <option value="-price">По цене(дорого)</option>
          <option value="price">По цене(дёшево)</option>
        </select>

        <div class="relative">
          <img class="absolute left-3 top-3" src="/search.svg" alt="" />
          <input
            @input="onSearchInput"
            class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
            placeholder="Поиск..."
            type="text"
          />
        </div>
      </div>
    </div>
    <div class="mt-5"></div>
    <Cardlist :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddPlus" />
  </div>
</template>
