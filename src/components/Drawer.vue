<script setup>
import { ref, computed, inject } from 'vue'
import axios from 'axios'

import DrawerHead from './DrawerHead.vue'
import CartItemList from './CartItemList.vue'
import InfoBlock from './InfoBlock.vue'

const props = defineProps({
  totalPrice: Number,
  vatPrice: Number
})

const { cart, closeDrawer } = inject('cart')

const isCreatingOrder = ref(false)
const orderId = ref(null)

const CreateOrder = async () => {
  try {
    isCreatingOrder.value = true

    const { data } = await axios.post(`https://2962b1b061b4abcd.mokky.dev/order`, {
      items: cart.value,
      totalPrice: props.totalPrice.value
    })

    cart.value = []

    orderId.value = data.id
    return data
  } catch (err) {
    console.log(err)
  } finally {
    isCreatingOrder.value = false
  }
}
const cartIsEmpty = computed(() => cart.value.length === 0)
const ButtonDisabled = computed(() => isCreatingOrder.value || cartIsEmpty.value)
</script>

<template>
  <div>
    <div @click="closeDrawer" class="fixed left-0 z-10 top-0 h-full w-full bg-black opacity-70" />
    <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
      <DrawerHead />

      <div v-if="!totalPrice || orderId" class="flex h-full items-center">
        <InfoBlock
          v-if="!totalPrice && !orderId"
          title="Корзина пуста"
          description="Добавьте хотя бы одну пару кросовок, чтобы сделать заказ"
          image-url="/package-icon.png"
        />
        <InfoBlock
          v-if="orderId"
          title="Заказ оформлен!"
          :description="`Ваш заказ №${orderId} скоро будет передан курьерской доставке`"
          image-url="/order-success-icon.png"
        />
      </div>
      <div v-else>
        <CartItemList />

        <div class="flex flex-col flex-1 justify-between mt-7">
          <div>
            <div class="flex flex-col gap-5">
              <div class="flex items-end gap-2">
                <span>Итого:</span>
                <div class="flex-1 border-b border-dashed" />
                <span class="font-bold">{{ totalPrice }} $</span>
              </div>

              <div class="flex items-end gap-2">
                <span>Налог 5%:</span>
                <div class="flex-1 border-b border-dashed" />
                <span class="font-bold">{{ vatPrice }} $</span>
              </div>
            </div>

            <button
              :disabled="ButtonDisabled"
              @click="CreateOrder"
              class="flex justify-center items-center disabled:bg-slate-300 gap-3 w-full py-3 mt-10 bg-lime-500 text-white rounded-xl transition active:bg-lime-700 hover:bg-lime-600"
            >
              Оформить заказ
              <img src="/arrow-next.svg" alt="Arrow" />
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
