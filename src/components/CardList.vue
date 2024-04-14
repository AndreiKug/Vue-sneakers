<script setup>
import Card from './Card.vue'

defineProps({
  items: Array,
  inFavorites: Boolean
})

const emit = defineEmits(['addToFavorite', 'addToCart'])
</script>

<template>
  <div class="grid grid-cols-4 gap-5" v-auto-animate>
    <Card
      v-for="item in items"
      :key="item.id"
      :id="item.id"
      :imageUrl="item.imageUrl"
      :title="item.title"
      :price="item.price"
      :onClickAdd="inFavorites ? null : () => emit('addToCart', item)"
      :onClickFavorite="inFavorites ? null : () => emit('addToFavorite', item)"
      :isFavorite="item.isFavorite"
      :isAdded="item.isAdded"
      :inFavorites="inFavorites"
    />
  </div>
</template>
