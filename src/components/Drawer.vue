<script setup>
import DrawerHead from './DrawerHead.vue';
import CartItemList from './CartItemList.vue';
</script>
<template>

<div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
<div class="fixed top-0 right-0 h-full w-96 bg-white z-20 p-10">
    <DrawerHead />
    <CartItemList />
    <div class="flex flex-col gap-2 mt-4">
        <div class="flex gap-2">
            <span>Total:</span>
            <div class="flex-1 border-b border-black border-dashed"></div>
            <b>$12990</b>
        </div>
        <div class="flex gap-2">
            <span>Tax 5%:</span>
            <div class="flex-1 border-b border-black border-dashed"></div>
            <b>$101</b>
        </div>
        <button disabled="true" class="mt-3 bg-lime-500 w-full rounded-xl py-3 text-white disabled:bg-slate-300 hover:bg-lime-600 active:bg-lime-700 transition cursor-pointer">
            Order
        </button>
    </div>
</div>
    
</template>