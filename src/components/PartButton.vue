<template>
    <div class="grid grid-cols-1 gap-4">
        <button v-for="parts in props.parts" :key="parts.id" @click="goToLog(parts.id)"
            class="bg-gray-800/70 border border-white/10 
            p-8 rounded-2xl text-2xl font-bold flex justify-between items-center active:scale-95 transition duration-150 tracking-widest">
            <span>{{ parts.label }}</span>
            <div class="flex justify-between items-center gap-6">
                <span :class="parts.icon"></span>
                <span class="icon-[material-symbols--chevron-right-rounded] text-4xl text-blue-500"></span>
            </div>
        </button>
    </div>
</template>

<script setup lang="ts">
import { useRouter } from 'vue-router';

const props = defineProps<{
    parts: { id: string; label: string; icon: string }[]
}>()
const router = useRouter() // 執行跳轉動作

const goToLog = (parts: string) => {
    //設定延遲顯示點擊後動畫
    setTimeout(() => {
        router.push(`/log/${parts}`)
    }, 150)
}

</script>