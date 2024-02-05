<script setup>

import Card from './Card.vue'

defineProps({
  items: Array
});

const emit = defineEmits(['addToFavorite', 'onClickAddPlus']);

</script>

<template>

    <div class="grid grid-cols-4 gap-5" v-auto-animate>
      <Card
        v-for="item in items"
        :key="item.id"
        :id="item.id"
        :title="item.title" 
        :price="item.price"
        :imageUrl="item.imageUrl"
        :isFavorite="item.isFavorite"
        :isAdded="item.isAdded"
        :onClickFavorite="() => emit('addToFavorite', item)"
        :onClickAdd="() => emit('onClickAddPlus', item)"
      />
    </div>

</template>