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
    <main class="flex-grow max-w-4xl mx-auto py-6 sm:px-6 lg:px-8 w-full flex">
      <!-- Sidebar for History List -->
      <aside class="w-64 bg-white rounded-lg shadow-lg p-6 mr-4 overflow-y-auto">
        <h2 class="text-xl font-bold text-gray-800 mb-4">Historial de Chats</h2>
        <button
          @click="startNewChat"
          class="w-full bg-green-500 text-white px-4 py-2 rounded hover:bg-green-600 transition-colors mb-4"
        >
          + Nuevo Chat
        </button>
        <div v-if="chatHistoryList && chatHistoryList.length > 0" class="space-y-3">
          <div
            v-for="chat in chatHistoryList"
            :key="chat._id"
            @click="loadChat(chat._id)"
            class="cursor-pointer hover:bg-gray-100 p-3 rounded-md transition-colors"
            :class="{'bg-red-100 border-l-4 border-red-500': chat._id === currentChatId}"
          >
            <h3 class="font-medium text-gray-800">{{ chat.title }}</h3>
            <p class="text-xs text-gray-500">{{ formatDate(chat.lastUpdated) }}</p>
          </div>
        </div>
        <div v-else class="text-center text-gray-500 text-sm">
          No hay chats guardados.
        </div>
      </aside>

      <!-- Chat Window and Input -->
      <div class="flex-grow flex flex-col">
        <!-- Chat Window -->
        <div ref="chatContainerRef" class="flex-grow bg-white rounded-lg shadow-lg p-6 overflow-y-auto mb-4 chat-container">
          <div v-for="message in messages" :key="message.timestamp + '-' + message.content" class="mb-6 message-wrapper">
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
          <div v-if="messages.length === 0 && !loading" class="text-center text-gray-500 welcome-message">
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
              :disabled="loading || !formData.question.trim()"
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
import { ref, onMounted, nextTick } from 'vue'

const messages = ref([])
const loading = ref(false)
const isLoading = ref(true)
const chatHistoryList = ref([]) // Para la lista de historiales
const currentChatId = ref(null) // Para el chat actual activo
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

const loadChatHistoryList = async () => {
  try {
    console.log('Attempting to fetch history list from /api/history');
    const response = await fetch('http://localhost:3001/api/history');
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    const data = await response.json(); // Get the raw data
    console.log('Raw data received from /api/history:', data);
    chatHistoryList.value = data; // Assign the data to the ref
    console.log('Assigned to chatHistoryList.value:', chatHistoryList.value);

    // Opcional: Cargar el primer chat si existe uno al cargar la lista
    if (chatHistoryList.value.length > 0 && !currentChatId.value) {
      loadChat(chatHistoryList.value[0]._id);
      console.log('Loading first chat from history list.');
    }

  } catch (error) {
    console.error('Error loading chat history list:', error);
    chatHistoryList.value = [];
  }
};

const loadChat = async (chatId) => {
  try {
     const response = await fetch(`http://localhost:3001/api/history/${chatId}`);
     if (!response.ok) {
       throw new Error(`HTTP error! status: ${response.status}`);
     }
     const chatData = await response.json();
     messages.value = chatData.conversation; // Cargar los mensajes del chat seleccionado
     currentChatId.value = chatId; // Establecer el chat activo
     console.log('Loaded chat:', chatData);

  } catch (error) {
    console.error(`Error loading chat with ID ${chatId}:`, error);
    // Opcional: mostrar un mensaje de error al usuario
  }
};

// Función para iniciar un nuevo chat (limpiar la vista actual)
const startNewChat = () => {
  messages.value = [];
  currentChatId.value = null;
  formData.value.question = ''; // Limpiar input también
};

const handleNewMessage = async () => {
  if (!formData.value.question.trim()) return

  const userMessageContent = formData.value.question.trim();

  // Agregar mensaje del usuario inmediatamente a la UI
  messages.value.push({
    role: 'user',
    content: userMessageContent,
    timestamp: new Date() // Usar timestamp local para la UI inmediata
  });

  // Guardar la pregunta actual y limpiar el input inmediatamente
  formData.value.question = ''

  loading.value = true
  try {
    // Enviar el historial completo (incluyendo el nuevo mensaje del usuario) y el chatId
    const messagesToSend = [
      systemMessage,
      // Asegurarse de enviar solo los mensajes de la conversación, no todo el objeto con ID, etc.
      ...messages.value.map(msg => ({ role: msg.role, content: msg.content }))
    ];

    const response = await fetch('http://localhost:3001/api/recognize', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({ 
        messages: messagesToSend,
        chatId: currentChatId.value // Enviar el ID del chat actual
      })
    })

    const responseData = await response.json()

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}, body: ${JSON.stringify(responseData)}`)
    }

    // Agregar la respuesta de la IA a la UI
    const aiMessage = {
       // Usar el timestamp del backend si es posible, o generar uno local
       role: 'assistant',
       content: responseData.aiResponse.details.mainInfo, // Corregido nuevamente para acceder a aiResponse
       timestamp: new Date() // Usar timestamp local por ahora, ajustar si el backend lo devuelve
    }
    messages.value.push(aiMessage)

    // Si el backend devolvió un nuevo chatId (indicando que se creó un nuevo chat)
    // Nota: Ahora el backend siempre debería devolver el chatId en la respuesta.
    if (responseData.chatId) {
       currentChatId.value = responseData.chatId;
       console.log('Chat ID received/updated:', currentChatId.value);
    }

    // Después de un envío exitoso (ya sea nuevo chat o actualización), recargar la lista de historiales
    loadChatHistoryList();

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
       id: Date.now() + 1, // ID temporal para la UI
       role: 'assistant',
       content: userErrorMessage,
       timestamp: new Date() // Timestamp local
    }
    messages.value.push(errorMessage)

  } finally {
    loading.value = false
     // Desplazar al final del chat después de cargar/recibir respuesta
     nextTick(() => {
       const chatContainer = document.querySelector('.chat-container');
       if (chatContainer) {
         chatContainer.scrollTop = chatContainer.scrollHeight;
       }
     });
  }
}

onMounted(() => {
  // Simular tiempo de carga inicial
  setTimeout(() => {
    isLoading.value = false;
    // Cargar la lista de historiales inmediatamente después de que termine la carga inicial
    loadChatHistoryList();
    console.log('onMounted finished, loading history list.');
  }, 2000);
});

// Eliminar la sección de historial de chats antiguos del template
// La restauraremos con el nuevo formato a continuación.
// <!-- Chat History Section -->
// <section class="max-w-4xl mx-auto py-6 sm:px-6 lg:px-8 w-full">
//   <div class="px-4 py-6 sm:px-0">
//     <h2 class="text-2xl font-bold text-gray-800 mb-4">Historial de Chats</h2>
//     <div class="bg-white rounded-lg shadow-lg p-6">
//       ...
//     </div>
//   </div>
// </section>
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
  display: flex; /* Habilitar flexbox */
  flex-direction: column; /* Apilar elementos verticalmente */
  justify-content: flex-start; /* Alinear elementos al inicio (parte superior) */
  overflow-y: auto; /* Asegura que la barra de scroll aparezca cuando sea necesario */
  flex-grow: 1; /* Asegura que ocupe el espacio disponible */
  padding: 1.5rem; /* Tailwind default p-6 */
  height: 0; /* Esencial para que flex-grow funcione correctamente con overflow */
  min-height: 300px; /* Valor fijo razonable, puedes ajustarlo */
}

.message-wrapper {
  opacity: 0;
  transform: translateY(20px);
  animation: messageAppear 0.3s ease forwards;
  margin-bottom: 1.5rem; /* Espacio entre mensajes */
}

/* Ajustes para el contenedor de mensajes AI para asegurar consistencia */
.ai-message,
.user-message {
    padding: 1rem; /* Tailwind default p-4 */
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
  margin-top: auto; /* Esto lo dejaré, ayuda si el chat está vacío a que el mensaje de bienvenida se quede abajo */
  padding-bottom: 1.5rem; /* Padding inferior para separarlo del input si el chat está vacío */
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

/* Asegurar que el contenedor principal de main permite a los hijos flex-grow y tiene altura */
main.flex {
    align-items: flex-start; /* Alinear los elementos hijos flex (sidebar y chat column) en la parte superior */
    min-height: 0; /* Necesario para que flex-grow en hijos funcione a veces */
    flex-grow: 1; /* Asegurar que el main crece */
    height: 100%; /* Asegurar que main ocupa la altura disponible de su padre (.min-h-screen) */
    display: flex; /* Asegurar que main es un contenedor flex */
    flex-direction: row; /* Los hijos (sidebar y chat column) están en fila */
}

/* Asegurar que la columna del chat y input ocupa el espacio disponible y tiene altura */
.flex-grow.flex.flex-col {
    flex-grow: 1;
    min-width: 0;
    min-height: 0; /* Necesario para que flex-grow en .chat-container funcione */
    height: 100%; /* Asegurar que la columna ocupa la altura de su padre main */
     display: flex; /* Asegurar que es un contenedor flex */
     flex-direction: column; /* Apilar hijos verticalmente */
}

/* Asegurar que el contenedor raíz tiene min-height 100vh y es flex column */
.min-h-screen {
    min-height: 100vh; /* Asegurar que el contenedor principal ocupa al menos toda la altura de la ventana */
    display: flex; /* Permitir que los hijos directos (header, main, footer) sean flexibles */
    flex-direction: column; /* Apilar header, main, footer verticalmente */
}

/* Asegurar que html y body tienen altura completa para que 100vh funcione correctamente */
html, body, #app {
    height: 100%;
    margin: 0;
    padding: 0;
    display: flex; /* Asegurar que son flex containers */
    flex-direction: column; /* Apilar verticalmente */
}

/* Ajustes para la barra lateral del historial */
aside.w-64 {
    /* La clase w-64 ya establece el ancho. Aseguramos la altura. */
    height: 100%; /* Ocupar la altura completa del contenedor flex padre (main.flex) */
    /* overflow-y: auto; ya está en el template, aseguramos que funcione con la altura */
    /* min-height: 0; */ /* Puede ser útil en algunos casos flex */
    box-sizing: border-box; /* Asegura que padding y border se incluyan en el tamaño */
}
</style> 