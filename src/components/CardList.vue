<script setup>
import { inject } from 'vue'
import Card from './Card.vue'

defineProps({
  items: Array
})

const onClickAdd = () => {
  alert("Add!")
}

const addToFavorite = inject('addToFavorite')

</script>

<template>

    <div class="grid grid-cols-4 gap-5">
      <Card
        v-for="item in items"
        :id="item.id"
        :title="item.title" 
        :price="item.price"
        :imageUrl="item.imageUrl"
        :isFavorite="item.isFavorite"
        :onClickFavorite="() => addToFavorite(item)"
        :onClickAdd="onClickAdd"
      />
    </div>

</template>