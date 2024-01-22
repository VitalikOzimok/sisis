<script setup>
import { RouterLink } from 'vue-router'
const emit = defineEmits(['openDrawer'])

defineProps({
  totalPrice: Number
})
</script>

<template>
  <header class="flex justify-between border-b border-slate-300 px-10 py-8">
    <RouterLink to="/">
      <div class="flex items-center gap-4">
        <img src="/logo.png" alt="Logo" class="w-10" />
        <div>
          <h2 class="text-xl font-bold uppercase">Vue Кросовки</h2>
          <p class="text-slate-400">Магазин лучших кросовок</p>
        </div>
      </div>
    </RouterLink>

    <ul class="flex items-center gap-10">
      <li
        @click="() => emit('openDrawer')"
        class="opacity-70 flex items-center gap-3 text-slate-500 cursor-pointer hover:opacity-100"
      >
        <img src="/cart.svg" alt="Cart" />
        <b>{{ totalPrice }} $</b>
      </li>
      <RouterLink to="/favorites">
        <li class="flex items-center gap-3 text-slate-500">
          <img src="/heart.svg" alt="Cart" />
          <b>Закладки</b>
        </li>
      </RouterLink>
      <RouterLink to="/profile">
        <li class="flex items-center gap-3 text-slate-500">
          <img src="/profile.svg" alt="Cart" />
          <b>Профиль</b>
        </li>
      </RouterLink>
    </ul>
  </header>
</template>
