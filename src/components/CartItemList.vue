<script setup>
import { inject } from 'vue'
import CartItem from './CartItem.vue'

const { cart, removeFromCart } = inject('cart')
</script>

<template>
<div class="flex flex-col gap-3 justify-between" v-auto-animate>
    <CartItem
        v-for="item in cart" 
        :key="item.id"
        :title="item.title"
        :image-url="item.imageUrl"
        :price="item.price"
        @remove-from-cart="() => removeFromCart(item)"
    />
</div>
</template>
