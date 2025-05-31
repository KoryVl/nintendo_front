<template>
  <div class="bg-white rounded-lg shadow-lg p-6 mt-6">
    <h2 class="text-2xl font-bold mb-6 text-gray-800">Historial de Exploración</h2>
    
    <div v-if="history.length > 0" class="space-y-4">
      <div
        v-for="item in history"
        :key="item._id"
        class="bg-white rounded-lg shadow p-4"
      >
        <div class="flex justify-between items-start">
          <div>
            <h3 class="text-lg font-semibold text-gray-800">
              {{ item.parameters.question }}
            </h3>
            <div class="mt-2 text-sm text-gray-600">
              <p v-if="item.parameters.era">Era: {{ item.parameters.era }}</p>
              <p v-if="item.parameters.franchise">Franquicia: {{ item.parameters.franchise }}</p>
              <p v-if="item.parameters.type">Tipo: {{ item.parameters.type }}</p>
            </div>
          </div>
          <button
            @click="selectedItem = item"
            class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 transition-colors"
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
      v-if="selectedItem"
      class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4"
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
          <!-- Parámetros de Búsqueda -->
          <div v-if="selectedItem.parameters.era || selectedItem.parameters.franchise || selectedItem.parameters.type">
            <h4 class="font-semibold text-gray-700">Parámetros de Búsqueda</h4>
            <div class="bg-gray-50 p-3 rounded">
              <p v-if="selectedItem.parameters.era"><span class="font-medium">Era:</span> {{ selectedItem.parameters.era }}</p>
              <p v-if="selectedItem.parameters.franchise"><span class="font-medium">Franquicia:</span> {{ selectedItem.parameters.franchise }}</p>
              <p v-if="selectedItem.parameters.type"><span class="font-medium">Tipo de Información:</span> {{ selectedItem.parameters.type }}</p>
              <p v-if="selectedItem.parameters.additionalDetails">
                <span class="font-medium">Detalles Adicionales:</span> {{ selectedItem.parameters.additionalDetails }}
              </p>
            </div>
          </div>

          <!-- Resultado -->
          <div>
            <h4 class="font-semibold text-gray-700">Resultado</h4>
            <div class="bg-gray-50 p-3 rounded">
              <div v-if="selectedItem.result.details && selectedItem.result.details.mainInfo">
                <p>{{ selectedItem.result.details.mainInfo }}</p>
              </div>
              <div v-else>
                <p class="text-gray-600">No hay información principal disponible para esta búsqueda.</p>
              </div>
            </div>
          </div>

          <!-- Fecha y Hora -->
          <div>
            <h4 class="font-semibold text-gray-700">Fecha y Hora</h4>
            <p class="text-gray-600">{{ new Date(selectedItem.timestamp).toLocaleString() }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const props = defineProps({
  history: {
    type: Array,
    default: () => []
  }
})

const selectedItem = ref(null)

const showDetails = (item) => {
  selectedItem.value = item
}

const closeDetails = () => {
  selectedItem.value = null
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