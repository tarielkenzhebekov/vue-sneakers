<script setup>
defineProps({
    id: Number,
    title: String,
    imageUrl: String,
    price: Number
});

const emit = defineEmits(['removeFromCart'])

</script>

<template>
    <div class="flex items-center border border-slate-200 p-4 rounded-xl gap-4">
        <img class="w-16 h-16" :src="imageUrl" :alt="title" />

        <div class="flex flex-col flex-1">
            <p>{{ title }}</p>

            <div class="flex justify-between mt-2">
                <b class="flex-1">{{ price }}</b>
                <img 
                    v-on:click="emit('removeFromCart')" 
                    class="opacity-60 hover:opacity-100 cursor-pointer transition" 
                    src="/close.svg" 
                />
            </div>
        </div>
    </div>
</template>