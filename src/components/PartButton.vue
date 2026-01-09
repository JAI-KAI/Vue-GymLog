<template>
    <div class="grid grid-cols-1 gap-4">
        <button v-for="(parts, index) in props.parts" :key="index" onclick="goToLog(parts)"
            class="bg-gray-800/70 backdrop-blur-md border border-white/10 
            p-8 rounded-2xl text-2xl font-bold flex justify-between items-center active:scale-95 transition tracking-widest">
            <span>{{parts}}部訓練</span> 
            <span class="icon-[material-symbols--chevron-right-rounded] text-4xl text-blue-500"></span>
        </button>
    </div>
</template>

<script setup lang="ts">
const props =  defineProps(['parts'])

</script>