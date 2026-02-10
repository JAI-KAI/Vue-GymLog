<template>
    <div class="flex-1 flex flex-col">
        <header class="flex items-center justify-between mb-6">
            <div class="flex items-center">
                <button @click="goBack()" class="p-2 text-blue-500">
                    <span
                        class="icon-[pixel--arrow-left-solid] text-2xl text-blue-500 align-middle active:scale-95 transition duration-150"></span>
                </button>
                <h2 id="categoryTitle" class="text-2xl font-bold ml-2">{{ currentLabel }}</h2>
            </div>
            <button @click="isModelShow = true" class="p-2 text-blue-500">
                <span class="icon-[pixel--plus-solid] text-2xl"></span>
            </button>
            
        </header>

        <InputModel 
        :part="part"  
        v-model:isVisible="isModelShow" 
        v-model:editingId="editingId"
        v-model:deleteId="deleteId" 
        @update-success="refreshData"/>

        <div class="flex-1 overflow-y-auto">
            <h3 class="text-gray-400 text-sm font-bold mb-3 uppercase tracking-widest flex justify-between">
                <span>歷史追蹤</span>
                <span id="recordCount" class="text-blue-500"></span>
            </h3>
            <div id="historyList" class="space-y-3 pb-20">
                <div v-for="r in filtered" :key="r.id"
                    class="bg-gray-800/70 border p-4 rounded-xl flex justify-between items-center border-l-4 border-blue-500 shadow-lg">
                    <div class="flex-1">
                        <div class="flex items-center gap-2">
                            <h4 class="font-bold text-lg text-white">{{ r.name }}</h4>
                            <div class="flex items-center gap-1.5 ml-auto">
                                <span class="relative flex h-2 w-2">
                                    <span
                                        class="animate-ping absolute inline-flex h-full w-full rounded-full  opacity-75"
                                        :class="isThisWeek(r.updatedAt) ? 'bg-green-400' : 'bg-gray-400'"></span>
                                    <span class="relative inline-flex rounded-full h-2 w-2"
                                        :class="isThisWeek(r.updatedAt) ? 'bg-green-500' : 'bg-gray-500'"></span>
                                </span>
                                <span @click="isThisWeek(r.updatedAt)"
                                    class="text-xs font-mono text-gray-300 leading-none">
                                    {{ formatTimestamp(r.updatedAt) }}
                                </span>
                            </div>
                        </div>
                        <div class="flex mt-1">
                            <span class="text-cyan-400 font-bold flex-[0.3]">{{ r.kg }}
                                <small class="text-[10px] opacity-70">kg</small>
                            </span>
                            <span class="text-amber-400 font-bold flex-[0.3]">{{ r.lb }}
                                <small class="text-[10px] opacity-70">lb</small>
                            </span>
                        </div>
                    </div>
                    <div class="flex gap-2 ml-4">
                        <button @click="editRecord(r.id)" class="p-2">
                            <span
                                class="icon-[pixelarticons--edit] text-xl text-gray-400 active:text-blue-400 align-middle"></span>
                        </button>
                        <button @click="deleteRecord(r.id)" class="p-2">
                            <span
                                class="icon-[pixelarticons--trash] text-xl text-gray-400 active:text-red-500 align-middle"></span>
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
import InputModel from '@/components/InputModel.vue';
import { computed, ref } from 'vue';
import { useRoute, useRouter } from 'vue-router';

const route = useRoute()
const router = useRouter()

// 取得部位顯示轉換中文
const part: string = route.params.parts as string
const categoryMap: Record<string, string> = {
    chest: '胸部訓練',
    back: '背部訓練',
    legs: '腿部訓練',
    shoulder: '肩部訓練',
    abdominals: '腹部訓練'
}
const currentLabel = categoryMap[part] || '未知部位'

//返回
const goBack = () => {
    setTimeout(() => {
        router.push('/')
    }, 150);
}

const isModelShow = ref(false)


//渲染資料
const records = ref<GymRecord[]>(JSON.parse(localStorage.getItem('gym_records') ?? '[]'))
const filtered = computed(() =>
    [...records.value]
        .filter(r => r.category == part)
        .sort((a, b) => b.updatedAt - a.updatedAt)
)

const refreshData = () => {
  records.value = JSON.parse(localStorage.getItem('gym_records') ?? '[]')
}

export interface GymRecord {
    id: number;
    category: string;
    name: string;
    kg: number;
    lb: number;
    updatedAt: number;
}

//時間顯示邏輯
const isThisWeek = (recordMs: number) => {
    if (!recordMs) return false;

    const now = Date.now();

    // 計算相差的毫秒數
    const diffInMs = now - recordMs;

    // 定義 7 天的毫秒數
    const sevenDaysInMs = 7 * 24 * 60 * 60 * 1000;

    return diffInMs >= 0 && diffInMs <= sevenDaysInMs
}

const formatTimestamp = (ts: number) => {
    return new Intl.DateTimeFormat('zh-TW', {
        month: 'numeric',
        day: 'numeric'
    }).format(ts); // 產出 "1/12" 日期格式
}



// 編輯 刪除
const editingId = ref<number | null>(null) 
const deleteId = ref<number | null>(null)

const editRecord = (id: number) => {
    editingId.value = id
    isModelShow.value = true
}

const deleteRecord = (id: number) => {
    deleteId.value = id
}

</script>