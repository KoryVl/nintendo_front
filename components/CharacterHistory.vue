<template>
  <div class="bg-white rounded-lg shadow-lg p-6 mt-6">
    <h2 class="text-2xl font-bold mb-6 text-gray-800">Historial de Exploración</h2>
    
    <div v-if="currentHistory" class="space-y-4">
      <div class="bg-white rounded-lg shadow p-4">
        <div class="flex justify-between items-start">
          <div>
            <h3 class="text-lg font-semibold text-gray-800">
              {{ currentHistory.question }}
            </h3>
            <div class="mt-2 text-sm text-gray-600">
              <p>Fecha: {{ formatDate(currentHistory.timestamp) }}</p>
            </div>
          </div>
          <button
            @click="showDetails"
            class="bg-red-500 text-white px-4 py-2 rounded hover:bg-red-600 transition-colors"
          >
            Ver detalles
          </button>
        </div>
      </div>
    </div>

    <div v-else class="text-center py-8">
      <p class="text-gray-500">No hay búsquedas anteriores</p>
    </div>

    <!-- Modal de Detalles -->
    <div
      v-if="isModalOpen"
      class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 z-50"
      @click="closeDetails"
    >
      <div
        class="bg-white rounded-lg p-6 max-w-2xl w-full max-h-[80vh] overflow-y-auto"
        @click.stop
      >
        <div class="flex justify-between items-start mb-4">
          <h3 class="text-xl font-bold text-gray-800">Detalles de la Búsqueda</h3>
          <button
            @click="closeDetails"
            class="text-gray-500 hover:text-gray-700"
          >
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
            </svg>
          </button>
        </div>

        <div class="space-y-4">
          <!-- Pregunta -->
          <div>
            <h4 class="font-semibold text-gray-700">Pregunta</h4>
            <div class="bg-gray-50 p-3 rounded">
              <p>{{ currentHistory.question }}</p>
            </div>
          </div>

          <!-- Respuesta -->
          <div>
            <h4 class="font-semibold text-gray-700">Respuesta</h4>
            <div class="bg-gray-50 p-3 rounded">
              <p>{{ currentHistory.response }}</p>
            </div>
          </div>

          <!-- Fecha y Hora -->
          <div>
            <h4 class="font-semibold text-gray-700">Fecha y Hora</h4>
            <p class="text-gray-600">{{ formatDate(currentHistory.timestamp) }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, defineProps, watch } from 'vue'

const props = defineProps({
  history: {
    type: Object,
    default: () => null
  }
})

const currentHistory = ref(null)
const isModalOpen = ref(false)

// Observar cambios en el historial
watch(() => props.history, (newHistory) => {
  if (newHistory) {
    currentHistory.value = {
      question: newHistory.question,
      response: newHistory.response,
      timestamp: new Date()
    }
  }
}, { immediate: true })

const formatDate = (date) => {
  return new Date(date).toLocaleString('es-ES', {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  })
}

const showDetails = () => {
  isModalOpen.value = true
}

const closeDetails = () => {
  isModalOpen.value = false
}
</script>

<style scoped>
button {
  font-family: var(--nintendo-font);
  font-size: 0.65rem;
  letter-spacing: 0.3px;
}

.modal-content {
  font-size: 0.65rem;
  line-height: 1.4;
}

.modal-content h3, .modal-content h4 {
  font-family: var(--nintendo-font);
  letter-spacing: 0.3px;
}

.timestamp {
  font-size: 0.6rem;
  opacity: 0.8;
}
</style> 