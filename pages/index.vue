<template>
  <div v-if="isLoading" class="fixed inset-0 bg-red-600 flex items-center justify-center z-50">
    <div class="text-center">
      <img src="/estrella.gif" alt="Super Mario Star Loading" class="mario-star-loader">
      <p class="text-white mt-4 text-xl">Cargando Nintendo Explorer...</p>
    </div>
  </div>

  <div v-else class="min-h-screen bg-gray-100 flex flex-col">
    <!-- Header -->
    <header class="bg-red-600 shadow-lg">
      <div class="max-w-7xl mx-auto py-4 px-4 sm:px-6 lg:px-8">
        <div class="flex items-center justify-center">
          <div class="flex items-center" :class="{'title-loaded': !isLoading}">
            <img src="/marioyyoshi.gif" alt="Mario and Yoshi" class="h-8 w-auto mr-2 yoshi-enter">
            <h1 class="text-3xl font-bold text-white title-animation">
              <span>N</span><span>i</span><span>n</span><span>t</span><span>e</span><span>n</span><span>d</span><span>o</span><span> </span><span>E</span><span>x</span><span>p</span><span>l</span><span>o</span><span>r</span><span>e</span><span>r</span><span> </span><span>C</span><span>h</span><span>a</span><span>t</span>
            </h1>
          </div>
        </div>
      </div>
    </header>

    <!-- Main Content -->
    <main class="flex-grow max-w-4xl mx-auto py-6 sm:px-6 lg:px-8 w-full">
      <div class="px-4 py-6 sm:px-0 h-full flex flex-col">
        <!-- Chat Window -->
        <div class="flex-grow bg-white rounded-lg shadow-lg p-6 overflow-y-auto mb-4 chat-container">
          <div v-for="message in messages" :key="message.id" class="mb-6 message-wrapper">
            <!-- Mensaje del Usuario -->
            <div v-if="message.role === 'user'" class="flex justify-end">
              <div class="bg-red-500 text-white rounded-2xl p-4 max-w-sm shadow-md message-bubble user-message">
                <p class="text-sm">{{ message.content }}</p>
              </div>
            </div>
            <!-- Mensaje de la IA -->
            <div v-else class="flex justify-start">
              <div class="bg-gray-100 rounded-2xl p-4 max-w-sm shadow-md message-bubble ai-message">
                <div class="flex items-center mb-2">
                  <img src="/mario-avatar.png" alt="Nintendo AI" class="w-6 h-6 mr-2">
                  <p class="font-semibold text-gray-800">Nintendo Explorer AI</p>
                </div>
                <p class="text-gray-700">{{ message.content }}</p>
              </div>
            </div>
          </div>

          <!-- Loading Animation -->
          <div v-if="loading" class="flex justify-start mb-6">
            <div class="bg-gray-100 rounded-2xl p-4 shadow-md message-bubble ai-message">
              <div class="flex items-center mb-2">
                <img src="/mario-avatar.png" alt="Nintendo AI" class="w-6 h-6 mr-2">
                <p class="font-semibold text-gray-800">Nintendo Explorer AI</p>
              </div>
              <div class="typing-indicator">
                <span></span>
                <span></span>
                <span></span>
              </div>
            </div>
          </div>

          <!-- Initial message if no history -->
          <div v-if="messages.length === 0" class="text-center text-gray-500 welcome-message">
            <p class="text-lg font-medium">¡Hola! Soy tu Nintendo Explorer AI. Pregúntame lo que quieras sobre la historia, juegos, personajes o consolas de Nintendo. 🎮✨</p>
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
              @keydown.enter.prevent="handleNewMessage"
              class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-red-500 focus:ring-red-500 sm:text-sm transition-all duration-200"
              placeholder="Ej: Cuéntame sobre los personajes principales de Super Mario 64..."
            ></textarea>
          </div>
          <div>
            <button
              type="submit"
              :disabled="loading"
              class="w-full flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-red-600 hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-500 transition-all duration-200 disabled:opacity-50 disabled:cursor-not-allowed"
            >
              <span v-if="!loading">Explorar</span>
              <span v-else class="flex items-center">
                <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                  <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                  <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                </svg>
                Procesando...
              </span>
            </button>
          </div>
        </form>
      </div>
    </main>

    <!-- Chat History Section -->
    <section class="max-w-4xl mx-auto py-6 sm:px-6 lg:px-8 w-full">
      <div class="px-4 py-6 sm:px-0">
        <h2 class="text-2xl font-bold text-gray-800 mb-4">Historial de Chats</h2>
        <div class="bg-white rounded-lg shadow-lg p-6">
          <div v-if="chatHistory.length > 0" class="space-y-2">
            <div v-for="chat in chatHistory" :key="chat.id" class="border border-gray-200 rounded-lg">
              <!-- Chat Header -->
              <div
                class="flex justify-between items-center p-4 cursor-pointer hover:bg-gray-50"
                @click="toggleChat(chat.id)"
              >
                <div class="flex items-center space-x-3">
                  <svg
                    class="w-5 h-5 transform transition-transform"
                    :class="{ 'rotate-90': expandedChats[chat.id] }"
                    fill="none"
                    stroke="currentColor"
                    viewBox="0 0 24 24"
                  >
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
                  </svg>
                  <div>
                    <h3 class="font-medium text-gray-900">
                      {{ getChatTitle(chat) }}
                    </h3>
                    <p class="text-sm text-gray-500">{{ formatDate(chat.timestamp) }}</p>
                  </div>
                </div>
              </div>

              <!-- Chat Content (Collapsible) -->
              <div
                v-show="expandedChats[chat.id]"
                class="border-t border-gray-200 bg-gray-50 p-4"
              >
                <div class="space-y-3">
                  <div v-for="message in chat.messages" :key="message.id" class="text-sm">
                    <div class="flex items-start space-x-2">
                      <span class="font-semibold min-w-[100px]">
                        {{ message.role === 'user' ? 'Tú' : 'Nintendo Explorer AI' }}:
                      </span>
                      <span class="text-gray-600">{{ message.content }}</span>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div v-else class="text-center text-gray-500 py-4">
            No hay chats guardados
          </div>
        </div>
      </div>
    </section>

    <!-- Footer -->
    <footer class="bg-red-600 text-white py-4 mt- auto">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="text-center text-sm text-white">
          Creado por Sofia uwu<br>
          Nintendo Explorer - Chat Mode
        </div>
      </div>
    </footer>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const messages = ref([])
const loading = ref(false)
const isLoading = ref(true)
const chatHistory = ref([])
const expandedChats = ref({})
const formData = ref({
  question: ''
})

// Initial system message to define AI's role
const systemMessage = {
  role: "system",
  content: "Eres un experto en la historia de Nintendo, sus consolas, juegos y personajes. Proporciona información precisa y concisa sobre Nintendo. Mantén tus respuestas breves y directas, limitándote a 2-3 oraciones por respuesta. Usa emojis apropiados para hacer las respuestas más amigables y divertidas, especialmente cuando hables de juegos, personajes o momentos emocionantes. Por ejemplo: 🎮 para juegos, 👾 para personajes, 🎲 para consolas, ⚔️ para aventuras, 🏰 para mundos, etc. Responde en español con un tono amigable y entusiasta. 🎮✨"
}

const formatDate = (timestamp) => {
  return new Date(timestamp).toLocaleString('es-ES', {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  })
}

const getChatTitle = (chat) => {
  const firstUserMessage = chat.messages.find(msg => msg.role === 'user')
  if (firstUserMessage) {
    const content = firstUserMessage.content
    return content.length > 50 ? content.substring(0, 50) + '...' : content
  }
  return 'Chat sin título'
}

const toggleChat = (chatId) => {
  expandedChats.value[chatId] = !expandedChats.value[chatId]
}

const saveChatToHistory = () => {
  if (messages.value.length > 0) {
    const chat = {
      id: Date.now(),
      timestamp: new Date(),
      messages: [...messages.value]
    }
    chatHistory.value.unshift(chat)
    if (chatHistory.value.length > 10) {
      chatHistory.value.pop() // Keep only the last 10 chats
    }
    localStorage.setItem('chatHistory', JSON.stringify(chatHistory.value))
  }
}

const loadChat = (chat) => {
  messages.value = [...chat.messages]
}

const handleNewMessage = async () => {
  if (!formData.value.question.trim()) return

  const userMessage = {
    id: Date.now(),
    role: 'user',
    content: formData.value.question
  }
  messages.value.push(userMessage)

  // Guardar la pregunta actual y limpiar el input inmediatamente
  const currentQuestion = formData.value.question
  formData.value.question = ''

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
    saveChatToHistory()

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
    saveChatToHistory()

  } finally {
    loading.value = false
  }
}

onMounted(() => {
  // Simular tiempo de carga inicial
  setTimeout(() => {
    isLoading.value = false
  }, 2000)

  // Eliminar la carga del historial guardado para que se reinicie con cada carga de página
  // const savedHistory = localStorage.getItem('chatHistory')
  // if (savedHistory) {
  //   chatHistory.value = JSON.parse(savedHistory)
  // }
})
</script>

<style scoped>
/* Estilos para la imagen de la estrella de Mario */
.mario-star-loader {
  width: 100px; /* Ajusta el tamaño según necesites */
  height: 100px;
  margin: 0 auto;
  animation: mario-star-spin 2s linear infinite; /* Animación de giro */
}

@keyframes mario-star-spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.rotate-90 {
  transform: rotate(90deg)
}

/* Estilos para la animación del título letra por letra */
.title-animation span {
  opacity: 0;
  /* Ajusta la duración de la transición si es necesario */
  transition: opacity 0.1s ease-in-out;
}

.title-loaded .title-animation span {
  opacity: 1;
}

/* Animación de entrada para el GIF de Mario y Yoshi */
.yoshi-enter {
  opacity: 0;
  transform: translateX(-20px); /* Inicia 20px a la izquierda */
  transition: opacity 0.8s ease-out, transform 0.8s ease-out; /* Duración de la transición */
}

/* Cuando el título se carga, Yoshi entra */
.title-loaded .yoshi-enter {
  opacity: 1;
  transform: translateX(0); /* Se mueve a su posición normal */
  /* Opcional: Añadir un pequeño retraso antes de que Yoshi entre si la carga es instantánea */
   transition-delay: 0.1s;
}


/* Retrasos para cada letra ajustados para después de la animación de Yoshi */
/* Asumiendo que la animación de Yoshi dura alrededor de 0.8s, empezamos los retrasos después de eso */
.title-loaded .title-animation span:nth-child(1) { transition-delay: 0.9s; }
.title-loaded .title-animation span:nth-child(2) { transition-delay: 0.95s; }
.title-loaded .title-animation span:nth-child(3) { transition-delay: 1.0s; }
.title-loaded .title-animation span:nth-child(4) { transition-delay: 1.05s; }
.title-loaded .title-animation span:nth-child(5) { transition-delay: 1.1s; }
.title-loaded .title-animation span:nth-child(6) { transition-delay: 1.15s; }
.title-loaded .title-animation span:nth-child(7) { transition-delay: 1.2s; }
.title-loaded .title-animation span:nth-child(8) { transition-delay: 1.25s; }
.title-loaded .title-animation span:nth-child(9) { transition-delay: 1.3s; } /* Espacio */
.title-loaded .title-animation span:nth-child(10) { transition-delay: 1.35s; }
.title-loaded .title-animation span:nth-child(11) { transition-delay: 1.4s; }
.title-loaded .title-animation span:nth-child(12) { transition-delay: 1.45s; }
.title-loaded .title-animation span:nth-child(13) { transition-delay: 1.5s; }
.title-loaded .title-animation span:nth-child(14) { transition-delay: 1.55s; }
.title-loaded .title-animation span:nth-child(15) { transition-delay: 1.6s; }
.title-loaded .title-animation span:nth-child(16) { transition-delay: 1.65s; }
.title-loaded .title-animation span:nth-child(17) { transition-delay: 1.7s; }
.title-loaded .title-animation span:nth-child(18) { transition-delay: 1.75s; } /* Espacio */
.title-loaded .title-animation span:nth-child(19) { transition-delay: 1.8s; }
.title-loaded .title-animation span:nth-child(20) { transition-delay: 1.85s; }
.title-loaded .title-animation span:nth-child(21) { transition-delay: 1.9s; }
.title-loaded .title-animation span:nth-child(22) { transition-delay: 1.95s; }

/* New styles for chat UI */
.chat-container {
  scroll-behavior: smooth;
}

.message-wrapper {
  opacity: 0;
  transform: translateY(20px);
  animation: messageAppear 0.3s ease forwards;
}

@keyframes messageAppear {
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.message-bubble {
  position: relative;
  transition: all 0.3s ease;
}

.message-bubble:hover {
  transform: translateY(-2px);
}

.user-message {
  background: linear-gradient(135deg, #ef4444 0%, #dc2626 100%);
}

.ai-message {
  background: linear-gradient(135deg, #f3f4f6 0%, #e5e7eb 100%);
}

/* Typing indicator animation */
.typing-indicator {
  display: flex;
  align-items: center;
  gap: 4px;
  padding: 8px 0;
}

.typing-indicator span {
  width: 8px;
  height: 8px;
  background: #6b7280;
  border-radius: 50%;
  animation: typing 1s infinite ease-in-out;
}

.typing-indicator span:nth-child(1) { animation-delay: 0.2s; }
.typing-indicator span:nth-child(2) { animation-delay: 0.3s; }
.typing-indicator span:nth-child(3) { animation-delay: 0.4s; }

@keyframes typing {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-5px); }
}

.welcome-message {
  animation: fadeIn 1s ease-out;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

/* Smooth transitions for all interactive elements */
button, textarea {
  transition: all 0.2s ease-in-out;
}

/* Hover effects for messages */
.message-bubble:hover {
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
}
</style> 