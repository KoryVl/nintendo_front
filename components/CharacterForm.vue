<template>
  <form @submit.prevent="onSubmit" class="space-y-4 bg-white p-6 rounded-lg shadow-lg">
    <h2 class="text-2xl font-bold text-gray-800">Nintendo Explorer</h2>
    
    <!-- Input de Pregunta -->
    <div>
      <label for="question" class="block text-sm font-medium text-gray-700">Tu Pregunta sobre Nintendo</label>
      <textarea
        id="question"
        v-model="formData.question"
        rows="3"
        required
        class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-red-500 focus:ring-red-500 sm:text-sm"
        placeholder="Ej: Cuéntame sobre los personajes principales de Super Mario 64..."
      ></textarea>
    </div>

    <!-- Botón de Envío -->
    <div>
      <button
        type="submit"
        class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-red-600 hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-500"
      >
        Explorar
      </button>
    </div>
  </form>
</template>

<script setup>
import { ref } from 'vue'

const emit = defineEmits(['submit'])

const formData = ref({
  question: '',
})

const onSubmit = () => {
  emit('submit', formData.value)
  // Clear the question after submission for a new turn
  formData.value.question = '';
}
</script>

<style scoped>
button {
  font-family: var(--nintendo-font);
  letter-spacing: 0.5px;
  text-transform: uppercase;
}

h2 {
   font-family: var(--nintendo-font);
   letter-spacing: 0.5px;
}
</style> 