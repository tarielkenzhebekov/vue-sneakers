<script setup>
defineProps({
  totalPrice: Number  
})
const emit = defineEmits(['openDrawer']);
</script>

<template>

<header class="flex justify-between border-b border-orange-200 px-10 py-8">
    <router-link to="/">
      <div class="flex items-center gap-4">
        <img src="/logo.png" alt="Logo" class="w-10" />
        <div>
            <h2 class="text-xl font-bold uppercase">Vue Sneakers</h2>
            <p class="text-slate-500">The best sneakers store</p>
        </div>
      </div>
    </router-link>

    <ul class="flex items-center gap-10">
      <li v-on:click="emit('openDrawer')" class="flex items-center gap-3 text-gray-500 cursor-pointer hover:text-black">
        <img src="/cart.svg" alt="Cart" />
        <b>${{ totalPrice }}</b>
      </li>
      <router-link to="/favorites">
        <li class="flex items-center gap-3 text-gray-500 cursor-pointer hover:text-black">
          <img src="/heart.svg" alt="Heart" />
          <span>Bookmarks</span>
        </li>
      </router-link>
      <li class="flex items-center gap-3 text-gray-500 cursor-pointer hover:text-black">
        <img src="/profile.svg" alt="Profile" />
        <span>Profile</span>
      </li>
    </ul>
  </header>

</template>