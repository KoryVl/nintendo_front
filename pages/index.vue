<template>
  <div class="min-h-screen bg-gray-100 flex flex-col">
    <!-- Header -->
    <header class="bg-red-600 shadow-lg">
      <div class="max-w-7xl mx-auto py-4 px-4 sm:px-6 lg:px-8">
        <div class="flex items-center justify-between">
          <h1 class="text-2xl font-bold text-white">Nintendo Explorer Chat</h1>
        </div>
      </div>
    </header>

    <!-- Main Content -->
    <main class="flex-grow max-w-4xl mx-auto py-6 sm:px-6 lg:px-8 w-full">
      <div class="px-4 py-6 sm:px-0 h-full flex flex-col">
        <!-- Chat Window -->
        <div class="flex-grow bg-white rounded-lg shadow-lg p-6 overflow-y-auto mb-4">
          <div v-for="message in messages" :key="message.id" class="mb-4">
            <div v-if="message.role === 'user'" class="flex justify-end">
              <div class="bg-blue-500 text-white rounded-lg p-3 max-w-sm">
                <p>{{ message.content }}</p>
              </div>
            </div>
            <div v-else class="flex justify-start">
              <div class="bg-gray-200 text-gray-800 rounded-lg p-3 max-w-sm">
                <p class="font-semibold mb-1">Nintendo Explorer AI</p>
                <p>{{ message.content }}</p>
              </div>
            </div>
          </div>
          <!-- Initial message if no history -->
           <div v-if="messages.length === 0" class="text-center text-gray-500">
             ¡Hola! Soy tu Nintendo Explorer AI. Pregúntame lo que quieras sobre la historia, juegos, personajes o consolas de Nintendo. 🎮✨
           </div>
        </div>

        <!-- Input Form -->
        <form @submit.prevent="handleNewMessage" class="space-y-4 bg-white p-6 rounded-lg shadow-lg">
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
          <div>
            <button
              type="submit"
              class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-red-600 hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-500"
            >
              Explorar
            </button>
          </div>
        </form>
      </div>
    </main>

    <!-- Footer -->
    <footer class="bg-gray-800 text-white py-4 mt- auto">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="text-center text-sm text-gray-400">
          Creado por Sofia uwu<br>
          Nintendo Explorer - Chat Mode
        </div>
      </div>
    </footer>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const messages = ref([])
const loading = ref(false)
const formData = ref({
  question: ''
})

// Initial system message to define AI's role
const systemMessage = {
  role: "system",
  content: "Eres un experto en la historia de Nintendo, sus consolas, juegos y personajes. Proporciona información precisa y concisa sobre Nintendo. Mantén tus respuestas breves y directas, limitándote a 2-3 oraciones por respuesta. Usa emojis apropiados para hacer las respuestas más amigables y divertidas, especialmente cuando hables de juegos, personajes o momentos emocionantes. Por ejemplo: 🎮 para juegos, 👾 para personajes, 🎲 para consolas, ⚔️ para aventuras, 🏰 para mundos, etc. Responde en español con un tono amigable y entusiasta. 🎮✨"
}

const handleNewMessage = async () => {
  if (!formData.value.question.trim()) return

  const userMessage = {
    id: Date.now(),
    role: 'user',
    content: formData.value.question
  }
  messages.value.push(userMessage)

  loading.value = true
  try {
    const messagesToSend = [
      systemMessage,
      ...messages.value.map(msg => ({ role: msg.role, content: msg.content }))
    ]

    const response = await fetch('http://localhost:3001/api/recognize', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ 
        messages: messagesToSend
      })
    })

    const responseData = await response.json()

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}, body: ${JSON.stringify(responseData)}`)
    }

    const aiMessage = {
      id: Date.now() + 1,
      role: 'assistant',
      content: responseData.details.mainInfo
    }
    messages.value.push(aiMessage)

  } catch (error) {
    console.error('Error:', error)
    
    let userErrorMessage = 'Lo siento, hubo un error al procesar tu pregunta. Por favor, intenta de nuevo.'
    try {
      const errorBody = JSON.parse(error.message.split('body: ')[1])
      if (errorBody && errorBody.message) {
         userErrorMessage = `Error del servidor: ${errorBody.message}`
      } else if (error.message) {
         userErrorMessage = `Error de red o HTTP: ${error.message}`
      }
    } catch (parseError) {
       console.error('Failed to parse error body:', parseError)
       userErrorMessage = `Error desconocido: ${error.message}`
    }

    const errorMessage = {
       id: Date.now() + 1,
       role: 'assistant',
       content: userErrorMessage
    }
    messages.value.push(errorMessage)

  } finally {
    loading.value = false
    formData.value.question = ''
  }
}
</script>

<style scoped>
.rotate-90 {
  transform: rotate(90deg)
}
</style> 