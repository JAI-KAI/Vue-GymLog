<template>
    <header class="mb-8 mt-4 text-center">
        <h1 class="text-3xl font-black text-blue-500 italic uppercase tracking-wider">GymLog</h1>
        <p class="text-gray-400 text-sm mt-1">選一個部位開始紀錄</p>
    </header>
    <PartButton :parts="parts"/>
    <BackupButton/>
</template>

<script setup lang="ts">
import BackupButton from '@/components/BackupButton.vue';
import PartButton from '@/components/PartButton.vue';

const parts = [
  { id: 'chest', label: '胸部訓練', icon: 'icon-[icon-park-outline--chest]' },
  { id: 'back', label: '背部訓練', icon: 'icon-[hugeicons--back-muscle-body]' },
  { id: 'legs', label: '腿部訓練', icon: 'icon-[hugeicons--body-part-leg]' },
  { id: 'shoulder', label: '肩部訓練', icon: 'icon-[hugeicons--shoulder]' },
  { id: 'abdominals', label: '腹部訓練', icon: 'icon-[hugeicons--body-part-six-pack]' }
];

</script>