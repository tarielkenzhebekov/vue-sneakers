<script setup>
defineProps({
    id: Number,
    title: String,
    imageUrl: String,
    price: Number,
    isFavorite: Boolean,
    isAdded: Boolean,
    onClickFavorite: Function,
    onClickAdd: Function
});
</script>

<template>

<div 
        class="relative bg-white border border-slate-200 rounded-3xl p-6 cursor-pointer 
            hover:-translate-y-2 hover:shadow-xl transition">
    <img
            v-if="onClickFavorite"
            v-on:click="onClickFavorite" 
            :src="isFavorite ? '/like-2.svg' : '/like-1.svg'" 
            class="absolute top-8 left-8" 
            alt="Like" />

    <img :src="imageUrl" alt="Sneaker 1" />
    <p class="mt-2">{{ title }}</p>

    <div class="flex justify-between mt-5">
        <div class="flex flex-col">
            <span class="text-slate-400">Price:</span>
            <b>${{price}}</b>
        </div>
        <img
                v-if="onClickAdd"
                v-on:click="onClickAdd" 
                :src="isAdded ? '/checked.svg' : '/plus.svg'" 
                alt="Plus" />
    </div>
</div>

</template>