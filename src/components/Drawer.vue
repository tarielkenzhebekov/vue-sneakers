<script setup>
import axios from 'axios';
import { inject, ref, computed } from 'vue';
import DrawerHead from './DrawerHead.vue';
import CartItemList from './CartItemList.vue';
import InfoBlock from './InfoBlock.vue';

const props = defineProps({
    totalPrice: Number,
    vatPrice: Number,
});

const { cart } = inject('cart');

const isCreatingOrder = ref(false);

const orderId = ref(null);

const buttonDisabled = computed(
  () => isCreatingOrder.value ? true : props.totalPrice ? false : true
);

const createOrder = async () => {
  try {
    isCreatingOrder.value = true;
    const { data } = await axios.post(`https://982d905e50f84a79.mokky.dev/orders`, {
      items: cart.value,
      totalPrice: props.totalPrice
    });

    cart.value = [];
    orderId.value = data.id;
  } catch(err) {
    console.log(err);
  } finally {
    isCreatingOrder.value = false;
  }
}

</script>
<template>

<div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
<div class="fixed top-0 right-0 h-full w-96 bg-white z-20 p-10">
    <DrawerHead />
    <div v-if="!totalPrice || orderId" class="flex h-full items-center">
        <InfoBlock
            v-if="!totalPrice && !orderId"
            imageUrl="/package-icon.png"
            title="Cart is empty"
            description="Please, add at least one pair of sneakers to order"
        />
        <InfoBlock
            v-if="orderId"
            imageUrl="/order-success-icon.png"
            title="Order is processed"
            :description="`Your order #${orderId} will be transferred to courier delivery soon`"
        />
    </div>
    <div v-else>
        <CartItemList/>
        <div class="flex flex-col gap-2 mt-4">
        <div class="flex gap-2">
            <span>Total:</span>
            <div class="flex-1 border-b border-black border-dashed"></div>
            <b>${{ totalPrice }}</b>
        </div>
        <div class="flex gap-2">
            <span>Tax 5%:</span>
            <div class="flex-1 border-b border-black border-dashed"></div>
            <b>${{ vatPrice }}</b>
        </div>
        <button 
            v-on:click="createOrder"
            :disabled="buttonDisabled" 
            class="mt-3 bg-lime-500 w-full rounded-xl py-3 text-white 
                disabled:bg-slate-300 hover:bg-lime-600 active:bg-lime-700 
                transition cursor-pointer">
            Order
        </button>
        </div>
    </div>
</div>
    
</template>