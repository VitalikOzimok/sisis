<script setup>
import axios from 'axios'
import { onMounted, ref } from 'vue'

import Cardlist from '@/components/Cardlist.vue'

const favorites = ref([])

onMounted(async () => {
  try {
    const { data } = await axios.get('https://2962b1b061b4abcd.mokky.dev/favorites')
    favorites.value = data.map((obj) => obj.item)
  } catch (err) {
    console.log(err)
  }
})
</script>

<template>
  <div>
    <h2 class="text-3xl font-bold mb-8">Мои закладки</h2>
    <Cardlist :items="favorites" is-favorites />
  </div>
</template>
