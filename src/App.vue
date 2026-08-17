<template>
  <div class="min-h-screen bg-gradient-to-br from-[#0a0a0a] via-[#121212] to-[#0a0a0a] text-white p-6 relative overflow-hidden">
    <!-- Gold-Effekt Hintergrund -->
    <div class="fixed inset-0 z-0 pointer-events-none">
      <div class="absolute top-1/4 left-1/3 w-[420px] h-[420px] bg-[#d4af37]/8 rounded-full filter blur-[110px]"></div>
      <div class="absolute bottom-1/4 right-1/3 w-[360px] h-[360px] bg-[#7b1e2b]/10 rounded-full filter blur-[90px]"></div>
    </div>

    <div class="max-w-3xl mx-auto z-10 relative">
      <!-- Kopfzeile -->
      <header class="text-center mb-10">
        <h1 class="text-5xl font-extrabold text-[#d4af37] drop-shadow-[0_0_20px_rgba(212,175,55,0.6)] mb-3 tracking-tight">
          📔 Tagebuch
        </h1>
        <p class="text-gray-400 text-sm drop-shadow-[0_0_8px_rgba(0,0,0,0.7)]">
          Deine Gedanken – sicher gespeichert im Browser
        </p>
      </header>

      <!-- Neueintrag -->
      <section class="mb-10">
        <div class="glass-card border border-[#d4af37]/20">
          <textarea
            v-model="newEntry"
            placeholder="Was liegt dir auf dem Herzen heute?"
            class="w-full bg-transparent border-2 border-[#d4af37]/30 rounded-xl p-5 md:p-6 text-xl md:text-2xl resize-y min-h-[160px] focus:outline-none focus:border-[#d4af37] transition-colors text-gray-200 placeholder-gray-500"
            :rows="5"
          ></textarea>
          <button
            @click="addEntry"
            :disabled="!newEntry.trim()"
            class="mt-4 w-full px-6 py-3 bg-gradient-to-r from-[#d4af37] to-[#b8860b] text-[#0a0a0a] font-bold rounded-xl hover:from-[#e6c259] hover:to-[#d4af37] disabled:opacity-40 disabled:cursor-not-allowed transition-all transform hover:scale-[1.02] shadow-[0_0_15px_rgba(212,175,55,0.5)]"
          >
            Eintrag speichern
          </button>
        </div>
      </section>

      <!-- Eintrageliste -->
      <section>
        <h2 class="text-lg font-semibold text-[#d4af37] mb-5 drop-shadow-[0_0_8px_rgba(212,175,55,0.4)]">
          Deine Einträge
        </h2>
        <div v-if="entries.length === 0" class="glass-card border border-white/10 p-8 md:p-10 rounded-xl text-center text-gray-400 text-lg">
          Noch keine Einträge. Schreibe oben einen hinein!
        </div>

        <div
          v-else
          v-for="entry in sortedEntries"
          :key="entry.id"
          class="glass-card rounded-xl p-5 mb-4 border border-white/10 hover:border-[#d4af37]/40 transition-all group"
        >
          <div class="flex justify-between items-start mb-3">
            <time class="text-sm text-[#d4af37]/80 font-medium">
              {{ formatDate(entry.date) }}
            </time>
            <button
              @click="deleteEntry(entry.id)"
              class="opacity-0 group-hover:opacity-100 text-red-400 hover:text-red-300 transition-opacity"
              title="Löschen"
            >✕</button>
          </div>
          <p class="text-lg md:text-xl text-gray-300 leading-relaxed whitespace-pre-wrap break-words">
            {{ entry.text }}
          </p>
        </div>
      </section>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const newEntry = ref('')
const entries = ref([])

onMounted(() => {
  const saved = localStorage.getItem('diary-entries')
  if (saved) entries.value = JSON.parse(saved)
})

const sortedEntries = computed(() =>
  [...entries.value].sort((a, b) => new Date(b.date) - new Date(a.date))
)

function addEntry() {
  if (!newEntry.value.trim()) return
  entries.value.unshift({ id: Date.now(), date: new Date().toISOString(), text: newEntry.value })
  saveToLocalStorage()
  newEntry.value = ''
}

function deleteEntry(id) {
  entries.value = entries.value.filter(e => e.id !== id)
  saveToLocalStorage()
}

function saveToLocalStorage() {
  localStorage.setItem('diary-entries', JSON.stringify(entries.value))
}

function formatDate(dateString) {
  return new Date(dateString).toLocaleDateString('de-DE', {
    weekday: 'long', year: 'numeric', month: 'long', day: 'numeric',
    hour: '2-digit', minute: '2-digit'
  })
}
</script>

<style scoped>
.glass-card {
  background: rgba(13, 17, 23, 0.65);
  backdrop-filter: blur(14px);
  -webkit-backdrop-filter: blur(14px);
  border: 1px solid rgba(212, 175, 55, 0.12);
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
}
</style>
