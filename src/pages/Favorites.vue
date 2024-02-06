<script setup>
import axios from 'axios';
import { onMounted, ref } from 'vue';
import CardList from '@/components/CardList.vue';

const favorites = ref([]);

onMounted(async () => {
    try {
        const { data } = await axios.get(`https://982d905e50f84a79.mokky.dev/favorites?_relations=items`)
        favorites.value = data.map((obj) => obj.item);
    } catch(err) {
        console.log(err);
    }
});
</script>

<template>
    <h2 class="text-3xl font-bold mb-6">My Favorites</h2>
    <CardList 
        :items="favorites"
        :favorites="true"
    />
</template>