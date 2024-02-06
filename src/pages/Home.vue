<script setup>
import axios from 'axios'
import { inject, reactive, ref, watch, onMounted } from 'vue';
import debounce from 'lodash.debounce';

import CardList from '../components/CardList.vue'

const items = ref([]);

const { cart, addToCart, removeFromCart } = inject('cart');

const filters = reactive({
  sortBy: 'id',
  searchQuery: ''
})

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value;
}

const onChangeSearchInput = debounce((event) => {
  filters.searchQuery = event.target.value;
}, 500);

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      item.isFavorite = true;

      const obj = {
        item_id: item.id
      };

      const { data } = await axios.post('https://982d905e50f84a79.mokky.dev/favorites', obj);
      item.favoriteId = data.id;
    } else {
      item.isFavorite = false;
      await axios.delete(`https://982d905e50f84a79.mokky.dev/favorites/${item.favoriteId}`);
      item.favoriteId = null;
    }
  } catch(err) {
    console.log(err);
  }
}

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item);
  } else {
    removeFromCart(item);
  }
}

const fetchFavorites = async () => {
  try {
    const { data : favorites } = await axios.get('https://982d905e50f84a79.mokky.dev/favorites')

    items.value = items.value.map(item => {
        const favorite = favorites.find(favorite => favorite.item_id === item.id);

        if (!favorite) {
          return item;
        }

        return {
          ...item,
          isFavorite: true,
          favoriteId: favorite.id
        }
    });

  } catch (err) {
    console.log(err)
  }
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

    items.value = data.map(obj => ({
      ...obj,
      isFavorite: false,
      isAdded: false,
      favoriteId : null
    }))

  } catch (err) {
    console.log(err)
  }
}

watch(filters, fetchItems);

watch(cart, () => {
  items.value = items.value.map(item => {
    if (!item.isAdded) {
      return item;
    }
    return {
      ...item,
      isAdded: false
    }
  })
})

onMounted(async () => {
  const localCart = localStorage.getItem('cart');
  cart.value = localCart ? JSON.parse(localCart) : [];
  
  await fetchItems();
  
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
  }))

  await fetchFavorites();
});

</script>

<template>
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
          <input 
            v-on:input="onChangeSearchInput" 
            class="border rounded-md py-2 pl-12 pr-4 outline-none focus:border-gray-400" 
            type="text"
            placeholder="Search..."/>
        </div>
      </div>
    </div>
    <div class="mt-6">
      <CardList 
        :items="items" 
        @add-to-favorite="addToFavorite" 
        @on-click-add-plus="onClickAddPlus" />
    </div>
</template>