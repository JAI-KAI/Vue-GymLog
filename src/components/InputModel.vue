<template>
    <div v-show="isVisible" class="p-6 absolute inset-0 z-50 bg-black/60 flex items-center justify-center">
        <div class="p-4 rounded-xl mb-6 border border-t-2 bg-gray-900 border-blue-500 w-full relative">
            <div class="space-y-4">
                <button id="cancelEditBtn" @click="closeModal"
                    class="block ms-auto  text-xs bg-gray-700 px-3 py-1 rounded-full text-gray-300 active:scale-95 transition duration-150">
                    取消
                </button>
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
                <button @click="saveRecord()"
                    class="w-full active:bg-blue-700 text-white font-bold py-3 rounded-lg transition active:scale-95 duration-150"
                    :class="editingId ? 'bg-green-600' : 'bg-blue-500'">{{ submitBtn }}</button>
            </div>
        </div>
    </div>
</template>

<script lang="ts" setup>
import type { GymRecord } from '@/views/LogView.vue';
import { computed, ref, watch } from 'vue';

const isVisible = defineModel<boolean>('isVisible')
const editingId = defineModel<null | number>('editingId')
const deleteId = defineModel<null | number>('deleteId')

const closeModal = () => {
    setTimeout(() => {
        resetForm()
        isVisible.value = false
    }, 150)
}

const props = defineProps(['part'])
const emit = defineEmits(['update-success'])

const records = ref<GymRecord[]>(JSON.parse(localStorage.getItem('gym_records') ?? '[]'))

// const editingId = ref<number | null>(null) // 追蹤目前正在編輯哪一筆
const submitBtn = computed(() => editingId.value ? '更新重量紀錄' : '新增這組紀錄')

// input area
const exerciseInput = ref('')
const weightInput = ref()
const unit = ref('kg')

//儲存按鈕
const isProcessing = ref(false) // 防止重複點擊

const saveRecord = () => {
    if (isProcessing.value) return
    setTimeout(() => {

        if (!exerciseInput.value || !weightInput.value) {
            alert('請填寫完整動作與重量');
            return;
        }
        isProcessing.value = true
        const updatedAt = Date.now();
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
            records.value = records.value.map(r => r.id === editingId.value ? { ...r, name: exerciseInput.value, kg, lb, updatedAt } : r);
            editingId.value = null;
        } else {
            // 新增模式
            const newRecord = {
                id: Date.now(),
                category: props.part,
                name: exerciseInput.value,
                kg,
                lb,
                updatedAt
            };
            records.value.push(newRecord);
        }

        localStorage.setItem('gym_records', JSON.stringify(records.value));
        resetForm();
        emit('update-success')
        closeModal()
        isProcessing.value = false
    }, 150);
}

const resetForm = () => {
    setTimeout(() => {
        exerciseInput.value = ''
        weightInput.value = null
        editingId.value = null
    }, 150);
}

//編輯 刪除
watch(editingId, (id) => {
    if (id) {
        const record = records.value.find(r => r.id === id);
        if (!record) return;
        editingId.value = id;
        exerciseInput.value = record.name;
        weightInput.value = record.kg;
        unit.value = 'kg';
    }
})
watch(deleteId, (id) => {
    if (id) {
        if (confirm('確定要刪除這條紀錄嗎？')) {
            records.value = records.value.filter(r => r.id !== id)
            localStorage.setItem('gym_records', JSON.stringify(records.value));
            emit('update-success')
        }
    }
    deleteId.value = null
})


</script>