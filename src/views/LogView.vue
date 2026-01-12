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
            <button v-show="editingId" id="cancelEditBtn" @click="resetForm()"
                class="text-xs bg-gray-700 px-3 py-1 rounded-full text-gray-300 active:scale-95 transition duration-150">取消編輯</button>
        </header>

        <div class="p-4 rounded-xl mb-6 border border-t-2 border-blue-500">
            <div class="space-y-4">
                <input v-model="exerciseInput" type="text" placeholder="動作名稱 (如：槓鈴臥推)"
                    class="w-full bg-gray-800 border-none rounded-lg p-3 text-white focus:outline-blue-500 focus:outline-2">
                <div class="flex gap-2">
                    <input v-model="weightInput" type="number" placeholder="輸入重量"
                        class="flex-1 bg-gray-800 border-none rounded-lg p-3 text-white focus:outline-blue-500 focus:outline-2">
                    <select v-model="unit" class="bg-gray-700 rounded-lg px-2 text-white font-bold">
                        <option value="kg">KG</option>
                        <option value="lb">LB</option>
                    </select>
                </div>
                <button id="submitBtn" @click="saveRecord()"
                    class="w-full active:bg-blue-700 text-white font-bold py-3 rounded-lg transition active:scale-95 duration-150"
                    :class="editingId ? 'bg-green-600' : 'bg-blue-500'">{{ submitBtn }}</button>
            </div>
        </div>

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
                            <span class="text-xs text-gray-500 font-mono bg-gray-800 px-1 rounded">{{ r.date }}</span>
                        </div>
                        <div class="flex gap-4 mt-1">
                            <span class="text-cyan-400 font-bold">{{ r.kg }} <small
                                    class="text-[10px] opacity-70">kg</small></span>
                            <span class="text-amber-400 font-bold">{{ r.lb }} <small
                                    class="text-[10px] opacity-70">lb</small></span>
                        </div>
                    </div>
                    <div class="flex gap-2">
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
import { computed, ref } from 'vue';
import { useRoute, useRouter } from 'vue-router';

const route = useRoute()
const router = useRouter()

// 取得部位顯示
const part: string = route.params.parts as string
const categoryMap: Record<string, string> = {
    chest: '胸部訓練',
    back: '背部訓練',
    legs: '腿部訓練',
    function: '功能性/核心訓練'
}
const currentLabel = categoryMap[part] || '未知部位'

//返回
const goBack = () => {
    setTimeout(() => {
        router.push('/')
    }, 150);
}

// input area
const exerciseInput = ref('')
const weightInput = ref()
const unit = ref('kg')
const editingId = ref<number | null>(null) // 追蹤目前正在編輯哪一筆
const submitBtn = computed(() =>
    editingId.value ? '更新重量紀錄' : '新增這組紀錄'
)

//渲染資料
const records = ref<GymRecord[]>(JSON.parse(localStorage.getItem('gym_records') ?? '[]'))
const filtered = computed(() =>
    records.value.filter(r => r.category == part)
)

interface GymRecord {
    id: number;
    category: string;
    name: string;
    kg: number;
    lb: number;
    date: string;
}

const saveRecord = () => {
    setTimeout(() => {
        const now = new Date();
        if (!exerciseInput.value || isNaN(weightInput.value)) {
            alert('請填寫完整動作與重量');
            return;
        }

        let kg: number, lb: number;
        if (unit.value === 'kg') {
            kg = +(weightInput.value.toFixed(1));
            lb = +(weightInput.value * 2.20462).toFixed(1);
        } else {
            lb = +(weightInput.value.toFixed(1));
            kg = +(weightInput.value / 2.20462).toFixed(1);
        }

        if (editingId.value) {
            // 更新模式
            records.value = records.value.map(r => r.id === editingId.value ? { ...r, name: exerciseInput.value, kg, lb, date: `${now.getMonth() + 1}/${now.getDate()}` } : r);
            editingId.value = null;
        } else {
            // 新增模式
            const newRecord = {
                id: Date.now(),
                category: part,
                name: exerciseInput.value,
                kg: kg,
                lb: lb,
                date: `${now.getMonth() + 1}/${now.getDate()}`
            };
            records.value.push(newRecord);
        }

        localStorage.setItem('gym_records', JSON.stringify(records.value));
        resetForm();
    }, 150);
}

const resetForm = () => {
    setTimeout(() => {
        exerciseInput.value = ''
        weightInput.value = null
        editingId.value = null
    }, 150);
}

// 編輯 刪除
const editRecord = (id: number) => {
    const record = records.value.find(r => r.id === id);
    if (!record) return;
    editingId.value = id;
    exerciseInput.value = record.name;
    weightInput.value = record.kg;
    unit.value = 'kg';
    // 捲動回頂部表單方便手機操作
    window.scrollTo({ top: 0, behavior: 'smooth' });
}

const deleteRecord = (id: number) => {
    if (confirm('確定要刪除這條紀錄嗎？')) {
        records.value = records.value.filter(r => r.id !== id)
        localStorage.setItem('gym_records', JSON.stringify(records.value));
        if (editingId.value == id) resetForm()
    }

}



</script>