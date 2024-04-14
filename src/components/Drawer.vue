<script setup>
import { computed, inject, ref } from 'vue'
import axios from 'axios'

import DrawerHead from './DrawerHead.vue'
import BasketListItem from './BasketListItem.vue'
import InfoBlock from './InfoBlock.vue'

const props = defineProps({
  totalPrice: Number,
  vatPrice: Number
})

const { cart } = inject('cart')

const isCreating = ref(false)
const orderId = ref(null)

const buttonDisabled = computed(() => isCreating.value || cartIsEmpty.value)
const cartIsEmpty = computed(() => cart.value.length === 0)

const createOrder = async () => {
  try {
    isCreating.value = true
    const { data } = await axios.post('https://395c355c1e8732ba.mokky.dev/orders', {
      items: cart.value,
      totalPrice: props.totalPrice.value
    })

    cart.value = []

    orderId.value = data.id
  } catch (error) {
    console.log(error)
  } finally {
    isCreating.value = false
  }
}
</script>

<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
    <div class="flex flex-col gap-5 h-full">
      <DrawerHead />

      <div v-if="!totalPrice || orderId" class="flex h-full items-center">
        <InfoBlock
          v-if="!totalPrice && !orderId"
          title="Корзина пустая"
          description="Добавьте хотя бы одну пару кроссовок, чтобы сделать заказ."
          image-url="/package-icon.png"
        />

        <InfoBlock
          v-if="orderId"
          title="Заказ оформлен"
          :description="`Ваш заказ #${orderId} скоро будет передан курьерской доставке`"
          image-url="/order-success-icon.png"
        />
      </div>

      <div v-else>
        <BasketListItem />

        <div class="flex flex-col gap-4 mt-7">
          <div class="flex gap-2">
            <span>Итого: </span>
            <div class="flex-1 border-b border-dashed"></div>
            <b>{{ totalPrice }} руб.</b>
          </div>

          <div class="flex gap-2">
            <span>Налог 5%: </span>
            <div class="flex-1 border-b border-dashed"></div>
            <b>{{ vatPrice }} руб.</b>
          </div>

          <button
            :disabled="buttonDisabled"
            @click="createOrder"
            class="bg-lime-500 w-full mt-4 rounded-xl py-3 text-white hover:bg-lime-600 transition active:bg-lime-700 disabled:bg-slate-300 cursor-pointer"
          >
            Оформить заказ
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
