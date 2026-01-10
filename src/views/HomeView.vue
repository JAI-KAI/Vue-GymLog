<template>
    <header class="mb-8 mt-4 text-center">
        <h1 class="text-3xl font-black text-blue-500 italic uppercase tracking-wider">GymLog</h1>
        <p class="text-gray-400 text-sm mt-1">選一個部位開始紀錄</p>
    </header>
    <PartButton :parts="parts"/>
</template>

<script setup lang="ts">
import PartButton from '@/components/PartButton.vue';

const parts = ['chest', 'back', 'legs', 'function']

</script>