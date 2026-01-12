<template>
  <div class="mt-10 p-4 border-t border-white/5">
    <h3 class="text-gray-500 text-xs font-bold mb-4 tracking-widest uppercase">資料安全備份</h3>

    <div class="space-y-3">
      <button @click="copyToClipboard"
        class="w-full bg-blue-500/10 border border-blue-500/20 text-blue-400 p-4 rounded-xl flex items-center justify-between active:scale-95 transition duration-150">
        <div class="flex items-center gap-3">
          <span class="icon-[material-symbols--content-copy-outline] text-xl"></span>
          <span class="font-bold">複製紀錄到備忘錄</span>
        </div>
        <span class="text-xs opacity-60">匯出</span>
      </button>

      <button @click="importFromText"
        class="w-full bg-gray-800 border border-white/5 text-gray-300 p-4 rounded-xl flex items-center justify-between active:scale-95 transition duration-150">
        <div class="flex items-center gap-3">
          <span class="icon-[material-symbols--settings-backup-restore-rounded] text-xl"></span>
          <span class="font-bold">從備忘錄還原紀錄</span>
        </div>
        <span class="text-xs opacity-60">匯入</span>
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">


const copyToClipboard = () => {
  setTimeout(async () => {
    const data = localStorage.getItem('gym_records')

    if (!data || data === '[]') {
      alert('目前沒有紀錄可以備份')
      return
    }

    try {
      await navigator.clipboard.writeText(data)
      alert('✅ 資料已轉換為代碼並複製！\n請直接貼到手機備忘錄存檔。')
    } catch (error) {
      alert(error + ': 複製失敗，請手動選取文字')
    }
  }, 150)
}

const importFromText = () => {
  setTimeout(() => {
    const text = window.prompt('請貼上您存在備忘錄的紀錄代碼：')
    if (!text) return

    try {
      const parsed = JSON.parse(text)
      if (!Array.isArray(parsed)) throw new Error()

      localStorage.setItem('gym_records', text)
      alert('還原紀錄成功！')
    } catch (error) {
      alert(error + ': ❌ 格式錯誤！請確保貼上的是完整的備份代碼。')
    }
  }, 150)

}

</script>