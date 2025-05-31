<template>
  <div class="bg-white rounded-lg shadow-lg p-6 mt-6">
    <div v-if="result" class="space-y-6">
      <!-- Información Detallada -->
      <div class="mt-6 space-y-4">
        <div v-if="result.details.mainInfo" class="bg-white rounded-lg p-4 shadow">
          <h3 class="text-lg font-semibold text-gray-800 mb-2">Información Principal</h3>
          <p class="text-gray-600">{{ result.details.mainInfo }}</p>
        </div>

        <div v-if="result.details.impact" class="bg-white rounded-lg p-4 shadow">
          <h3 class="text-lg font-semibold text-gray-800 mb-2">Impacto Cultural</h3>
          <p class="text-gray-600">{{ result.details.impact }}</p>
        </div>

        <div v-if="result.details.curiosities" class="bg-white rounded-lg p-4 shadow">
          <h3 class="text-lg font-semibold text-gray-800 mb-2">Curiosidades</h3>
          <p class="text-gray-600">{{ result.details.curiosities }}</p>
        </div>
      </div>

      <!-- Nivel de Confianza -->
      <div class="bg-green-50 p-4 rounded-lg">
        <div class="flex items-center">
          <div class="w-full bg-gray-200 rounded-full h-2.5">
            <div
              class="bg-green-600 h-2.5 rounded-full"
              :style="{ width: `${result.confidence * 100}%` }"
            ></div>
          </div>
          <span class="ml-4 text-green-700 font-medium">{{ (result.confidence * 100).toFixed(1) }}%</span>
        </div>
      </div>
    </div>

    <div v-else class="text-center py-8">
      <p class="text-gray-500">Haz una pregunta sobre Nintendo para obtener información</p>
    </div>
  </div>
</template>

<script setup>
defineProps({
  result: {
    type: Object,
    default: null
  }
})
</script>

<style scoped>
.prose {
  font-size: 0.65rem;
  line-height: 1.4;
}

ul {
  list-style-type: none;
  padding-left: 0;
}

li {
  margin-bottom: 0.4rem;
  position: relative;
  padding-left: 1.2rem;
}

li:before {
  content: "►";
  position: absolute;
  left: 0;
  color: #ef4444;
  font-size: 0.6rem;
}
</style> 