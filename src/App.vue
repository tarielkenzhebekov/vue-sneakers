<script setup>
import { onMounted, ref, reactive, watch, provide, computed } from 'vue'
import axios from 'axios'

import Header from './components/Header.vue'
import CardList from './components/CardList.vue'
import Drawer from './components/Drawer.vue'

const items = ref([]);

const cart = ref([]);

const isCreatingOrder = ref(false);

const totalPrice = computed(
  () => cart.value.reduce((acc, item) => acc + item.price, 0)
);

const vatPrice = computed(
  () => Math.round(totalPrice.value * 0.05)
);

const cartButtonDisabled = computed(
  () => isCreatingOrder.value ? true : totalPrice.value ? false : true
);

const drawerOpen = ref(false);

const closeDrawer = () => {
  drawerOpen.value = false;
}

const openDrawer = () => {
  drawerOpen.value = true;
}

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

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      item.isFavorite = true;

      const obj = {
        productId: item.id
      };

      const { data } = await axios.post('https://982d905e50f84a79.mokky.dev/favorites', obj);
      console.log(data);
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

const addToCart = (item) => {
  cart.value.push(item);
  item.isAdded = true;
}

const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1);
  item.isAdded = false;
}

const createOrder = async () => {
  try {
    isCreatingOrder.value = true;
    const { data } = await axios.post(`https://982d905e50f84a79.mokky.dev/orders`, {
      items: cart.value,
      totalPrice: totalPrice.value
    });

    cart.value = [];

    items.value = items.value.map(item => {
      if (!item.isAdded) {
        return item;
      }
      return {
        ...item,
        isAdded: false
      }
    });
    
    return data;
  } catch(err) {
    console.log(err);
  } finally {
    isCreatingOrder.value = false;
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
        const favorite = favorites.find(favorite => favorite.productId === item.id);

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

watch(filters, fetchItems);

watch(
  cart,
  () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  { deep : true }
)

provide('cart', {
  cart,
  closeDrawer,
  removeFromCart
})

</script>

<template>

<Drawer v-if="drawerOpen" 
  :total-price="totalPrice"
  :vat-price="vatPrice"
  :button-disabled="cartButtonDisabled"
  @create-order="createOrder"
/>

<div class="bg-white w-4/5 m-auto rounded-2xl shadow-2xl mt-14">
  <Header :total-price="totalPrice" @open-drawer="openDrawer" />
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
  </div>
</div>

</template>

<style scoped>

</style>
