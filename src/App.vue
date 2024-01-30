<script setup>
import { onMounted, ref, reactive, watch } from 'vue'
import axios from 'axios'

import Header from './components/Header.vue'
import CardList from './components/CardList.vue'

const items = ref([]);

const filters = reactive({
  sortBy: 'id',
  searchQuery: ''
})

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value;
}

const onChangeSearchInput = (event) => {
  filters.searchQuery = event.target.value;
}

const fetchItems = async() => {
  try {
    const params = {
      sortBy: filters.sortBy,
    }

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`;
    }

    const { data } = await axios.get('https://982d905e50f84a79.mokky.dev/items', {
      params
    })

    items.value = data

  } catch (err) {
    console.log(err)
  }
}

onMounted(fetchItems)
watch(filters, fetchItems);

</script>

<template>

<div class="bg-white w-4/5 m-auto rounded-2xl shadow-2xl mt-14">
  <Header />
  <div class="p-10">
    <div class="flex justify-between items-center">
      <h2 class="text-3xl font-bold mb-6">All Sneakers</h2>

      <div class="flex gap-5">
        <select v-on:change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
          <option value="id">Sort by (default)</option>
          <option value="title">By name</option>
          <option value="price">By Price (cheaper first)</option>
          <option value="-price">By price (expensive first)</option>
        </select>

        <div class="relative">
          <img class="absolute left-4 top-3" src="/search.svg"/>
          <input v-on:input="onChangeSearchInput" class="border rounded-md py-2 pl-12 pr-4 outline-none focus:border-gray-400" type="text" placeholder="Search..."/>
        </div>
      </div>
    </div >
    <div class="mt-6">
      <CardList :items="items"/>
    </div>
  </div>
</div>

</template>

<style scoped>

</style>
