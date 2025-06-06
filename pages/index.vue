<!-- pages/chat.vue -->
<template>
  <div
    class="flex flex-col min-h-screen bg-gradient-to-br from-slate-900 via-blue-900 to-slate-900"
  >
    <!-- Background Effects -->
    <div class="absolute inset-0 opacity-20"></div>

    <!-- Header -->
    <header
      class="relative z-10 px-4 py-4 border-b bg-white/10 backdrop-blur-lg border-white/20"
    >
      <div class="flex items-center justify-between max-w-4xl mx-auto">
        <div class="flex items-center space-x-4">
          <div class="relative">
            <div
              class="flex items-center justify-center w-12 h-12 shadow-lg bg-gradient-to-r from-blue-500 to-purple-600 rounded-2xl"
            >
              <svg
                class="w-6 h-6 text-white"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z"
                />
              </svg>
            </div>
            <div
              class="absolute w-4 h-4 bg-green-400 border-2 rounded-full -bottom-1 -right-1 border-slate-900 animate-pulse"
            ></div>
          </div>
          <div>
            <h1 class="text-xl font-bold text-white">AI Assistant</h1>
            <p class="text-sm text-slate-300">
              {{ isTyping ? "Typing..." : "Online" }}
            </p>
          </div>
        </div>

        <div class="flex items-center space-x-2">
          <button
            @click="clearChat"
            class="p-2 transition-all duration-200 text-slate-400 hover:text-white hover:bg-white/10 rounded-xl"
            title="Clear chat"
          >
            <svg
              class="w-5 h-5"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"
              />
            </svg>
          </button>
          <button
            @click="toggleTheme"
            class="p-2 transition-all duration-200 text-slate-400 hover:text-white hover:bg-white/10 rounded-xl"
            title="Toggle theme"
          >
            <svg
              v-if="isDark"
              class="w-5 h-5"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z"
              />
            </svg>
            <svg
              v-else
              class="w-5 h-5"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z"
              />
            </svg>
          </button>
        </div>
      </div>
    </header>

    <!-- Chat Messages -->
    <main class="relative z-10 flex-1 overflow-hidden">
      <div
        ref="chatContainer"
        class="h-full px-4 py-6 space-y-4 overflow-y-auto scrollbar-thin scrollbar-thumb-white/20 scrollbar-track-transparent"
      >
        <div class="max-w-4xl mx-auto">
          <!-- Welcome Message -->
          <div v-if="messages.length === 0" class="py-12 text-center">
            <div
              class="flex items-center justify-center w-20 h-20 mx-auto mb-6 shadow-xl bg-gradient-to-r from-blue-500 to-purple-600 rounded-3xl"
            >
              <svg
                class="w-10 h-10 text-white"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z"
                />
              </svg>
            </div>
            <h2 class="mb-4 text-2xl font-bold text-white">
              Welcome to AI Assistant
            </h2>
            <p class="mb-8 text-slate-300">
              I'm here to help you with any questions or tasks. What would you
              like to know?
            </p>

            <!-- Quick Actions -->
            <div
              class="grid max-w-2xl grid-cols-1 gap-4 mx-auto md:grid-cols-3"
            >
              <button
                v-for="suggestion in quickSuggestions"
                :key="suggestion.text"
                @click="sendMessage(suggestion.text)"
                class="p-4 text-left transition-all duration-300 transform border bg-white/10 backdrop-blur-sm rounded-2xl border-white/20 hover:bg-white/20 hover:scale-105"
              >
                <div class="mb-2 text-2xl">{{ suggestion.icon }}</div>
                <div class="mb-1 font-medium text-white">
                  {{ suggestion.title }}
                </div>
                <div class="text-sm text-slate-300">
                  {{ suggestion.description }}
                </div>
              </button>
            </div>
          </div>

          <!-- Messages -->
          <TransitionGroup name="message" tag="div" class="space-y-4">
            <div
              v-for="message in messages"
              :key="message.id"
              :class="[
                'flex',
                message.sender === 'user' ? 'justify-end' : 'justify-start',
              ]"
            >
              <div
                :class="[
                  'max-w-xs md:max-w-md lg:max-w-lg xl:max-w-xl flex',
                  message.sender === 'user' ? 'flex-row-reverse' : 'flex-row',
                ]"
              >
                <!-- Avatar -->
                <div
                  :class="[
                    'flex-shrink-0 w-10 h-10 rounded-2xl flex items-center justify-center shadow-lg',
                    message.sender === 'user'
                      ? 'bg-gradient-to-r from-purple-500 to-pink-500 ml-3'
                      : 'bg-gradient-to-r from-blue-500 to-purple-600 mr-3',
                  ]"
                >
                  <svg
                    v-if="message.sender === 'user'"
                    class="w-5 h-5 text-white"
                    fill="none"
                    stroke="currentColor"
                    viewBox="0 0 24 24"
                  >
                    <path
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      stroke-width="2"
                      d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z"
                    />
                  </svg>
                  <svg
                    v-else
                    class="w-5 h-5 text-white"
                    fill="none"
                    stroke="currentColor"
                    viewBox="0 0 24 24"
                  >
                    <path
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      stroke-width="2"
                      d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z"
                    />
                  </svg>
                </div>

                <!-- Message Bubble -->
                <div
                  :class="[
                    'rounded-3xl px-6 py-4 shadow-lg backdrop-blur-sm border transition-all duration-300 hover:shadow-xl',
                    message.sender === 'user'
                      ? 'bg-gradient-to-r from-purple-500 to-pink-500 text-white border-purple-400/50'
                      : 'bg-white/10 text-white border-white/20',
                  ]"
                >
                  <p class="whitespace-pre-wrap">{{ message.text }}</p>
                  <div
                    :class="[
                      'text-xs mt-2 opacity-70',
                      message.sender === 'user'
                        ? 'text-purple-100'
                        : 'text-slate-300',
                    ]"
                  >
                    {{ formatTime(message.timestamp) }}
                  </div>
                </div>
              </div>
            </div>
          </TransitionGroup>

          <!-- Typing Indicator -->
          <div v-if="isTyping" class="flex justify-start">
            <div class="flex max-w-xs md:max-w-md lg:max-w-lg xl:max-w-xl">
              <div
                class="flex items-center justify-center flex-shrink-0 w-10 h-10 mr-3 shadow-lg rounded-2xl bg-gradient-to-r from-blue-500 to-purple-600"
              >
                <svg
                  class="w-5 h-5 text-white"
                  fill="none"
                  stroke="currentColor"
                  viewBox="0 0 24 24"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z"
                  />
                </svg>
              </div>
              <div
                class="px-6 py-4 border bg-white/10 backdrop-blur-sm rounded-3xl border-white/20"
              >
                <div class="flex space-x-1">
                  <div
                    class="w-2 h-2 rounded-full bg-white/60 animate-bounce"
                  ></div>
                  <div
                    class="w-2 h-2 rounded-full bg-white/60 animate-bounce"
                    style="animation-delay: 0.1s"
                  ></div>
                  <div
                    class="w-2 h-2 rounded-full bg-white/60 animate-bounce"
                    style="animation-delay: 0.2s"
                  ></div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>

    <!-- Input Area -->
    <footer
      class="w-full px-4 py-4 border-t bg-white/10 backdrop-blur-xl border-white/20"
    >
      <div class="max-w-4xl mx-auto">
        <form @submit.prevent="handleSubmit" class="flex space-x-4">
          <div class="relative flex-1">
            <textarea
              v-model="currentMessage"
              ref="messageInput"
              maxlength="1000"
              @keydown.enter.exact.prevent="handleSubmit"
              @keydown.enter.shift.exact="addNewLine"
              placeholder="Type your message... (Shift+Enter for new line)"
              rows="1"
              class="w-full px-6 py-4 overflow-hidden text-white transition-all duration-300 border resize-none bg-white/10 border-white/20 rounded-2xl placeholder-slate-300 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent backdrop-blur-sm"
              :disabled="isTyping"
            ></textarea>

            <!-- Character count -->
            <div class="absolute text-xs bottom-2 right-5 text-slate-400">
              {{ currentMessage.length }}/1000
            </div>
          </div>

          <button
            type="submit"
            :disabled="!currentMessage.trim() || isTyping"
            class="flex items-center px-6 py-4 space-x-2 text-white transition-all duration-300 transform shadow-lg bg-gradient-to-r from-blue-500 to-purple-600 rounded-2xl hover:from-blue-600 hover:to-purple-700 disabled:opacity-50 disabled:cursor-not-allowed hover:scale-105"
          >
            <svg
              v-if="!isTyping"
              class="w-5 h-5"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M12 19l9 2-9-18-9 18 9-2zm0 0v-8"
              />
            </svg>
            <svg
              v-else
              class="w-5 h-5 animate-spin"
              fill="none"
              viewBox="0 0 24 24"
            >
              <circle
                class="opacity-25"
                cx="12"
                cy="12"
                r="10"
                stroke="currentColor"
                stroke-width="4"
              ></circle>
              <path
                class="opacity-75"
                fill="currentColor"
                d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
              ></path>
            </svg>
            <span class="hidden sm:inline">{{
              isTyping ? "Sending..." : "Send"
            }}</span>
          </button>
        </form>

        <!-- Quick Reply Buttons -->
        <div v-if="quickReplies.length > 0" class="flex flex-wrap gap-2 mt-3">
          <button
            v-for="reply in quickReplies"
            :key="reply"
            @click="sendMessage(reply)"
            class="px-4 py-2 text-sm transition-all duration-200 border bg-white/5 border-white/20 rounded-xl text-slate-300 hover:bg-white/10 hover:text-white"
          >
            {{ reply }}
          </button>
        </div>
      </div>
    </footer>
  </div>
</template>

<script setup>
import axios from "axios";

// Meta tags for SEO
useHead({
  title: "AI Chatbot - Intelligent Assistant",
  meta: [
    {
      name: "description",
      content: "Chat with our AI assistant built with Nuxt.js and Tailwind CSS",
    },
  ],
});

// Reactive data
const messages = ref([]);
const currentMessage = ref("");
const isTyping = ref(false);
const isDark = ref(true);
const chatContainer = ref(null);
const messageInput = ref(null);

// Quick suggestions for new users
const quickSuggestions = ref([
  {
    icon: "💡",
    title: "Get Ideas",
    description: "Brainstorm creative solutions",
    text: "Help me brainstorm some creative ideas for my project",
  },
  {
    icon: "📚",
    title: "Learn Something",
    description: "Explain complex topics",
    text: "Explain quantum computing in simple terms",
  },
  {
    icon: "🔧",
    title: "Solve Problems",
    description: "Get help with challenges",
    text: "Help me debug my code or solve a problem",
  },
]);

// Quick reply suggestions
const quickReplies = ref([
  "Tell me more",
  "That's helpful",
  "Can you explain further?",
  "What else?",
  "Thanks!",
]);

// const sendMessage = async (text = null) => {
//   const messageText = text || currentMessage.value.trim();
//   if (!messageText) return;

//   // Add user message
//   const userMessage = {
//     id: Date.now(),
//     text: messageText,
//     sender: "user",
//     timestamp: new Date(),
//   };

//   messages.value.push(userMessage);
//   currentMessage.value = "";

//   // Auto-resize textarea
//   nextTick(() => {
//     if (messageInput.value) {
//       messageInput.value.style.height = "auto";
//     }
//   });

//   // Scroll to bottom
//   scrollToBottom();

//   // Simulate AI typing
//   isTyping.value = true;

//   // Simulate AI response delay
//   await new Promise((resolve) =>
//     setTimeout(resolve, 1000 + Math.random() * 2000)
//   );

//   // Generate AI response (in real app, this would call your AI API)
//   const aiResponse = {
//     id: Date.now() + 1,
//     text: generateAIResponse(messageText),
//     sender: "ai",
//     timestamp: new Date(),
//   };

//   isTyping.value = false;
//   messages.value.push(aiResponse);

//   // Scroll to bottom
//   scrollToBottom();

//   // Save to localStorage
//   saveMessages();
// };

const sendMessage = async (text = null) => {
  const messageText = text || currentMessage.value.trim();
  if (!messageText) return;

  // Add user message
  const userMessage = {
    id: Date.now(),
    text: messageText,
    sender: "user",
    timestamp: new Date(),
  };

  messages.value.push(userMessage);
  currentMessage.value = "";

  // Auto-resize textarea
  nextTick(() => {
    if (messageInput.value) {
      messageInput.value.style.height = "auto";
    }
  });

  // Scroll to bottom
  scrollToBottom();

  // Simulate AI typing
  isTyping.value = true;

  try {
    // Send message to backend (adjust payload as needed)
    const response = await axios.post(
      "https://n8n-87h0.onrender.com/webhook/chat",
      {
        message: messageText,
      }
    );

    // Use response from backend or fallback
    const aiResponse = {
      id: Date.now() + 1,
      text: response.data?.output,
      sender: "ai",
      timestamp: new Date(),
    };

    messages.value.push(aiResponse);
  } catch (error) {
    // Handle error and show fallback message
    const aiResponse = {
      id: Date.now() + 1,
      text: "",
      sender: "ai",
      timestamp: new Date(),
    };
    messages.value.push(aiResponse);
  } finally {
    isTyping.value = false;
    scrollToBottom();
    saveMessages();
  }
};

const handleSubmit = () => {
  if (currentMessage.value.trim() && !isTyping.value) {
    sendMessage();
  }
};

const addNewLine = () => {
  currentMessage.value += "\n";
};

const clearChat = () => {
  messages.value = [];
  localStorage.removeItem("nuxt-chat-messages");
};

const toggleTheme = () => {
  isDark.value = !isDark.value;
  // In a real app, you might want to apply theme changes
};

const scrollToBottom = () => {
  nextTick(() => {
    if (chatContainer.value) {
      chatContainer.value.scrollTop = chatContainer.value.scrollHeight;
    }
  });
};

const formatTime = (timestamp) => {
  return new Date(timestamp).toLocaleTimeString([], {
    hour: "2-digit",
    minute: "2-digit",
  });
};

const saveMessages = () => {
  localStorage.setItem("nuxt-chat-messages", JSON.stringify(messages.value));
};

const loadMessages = () => {
  const saved = localStorage.getItem("nuxt-chat-messages");
  if (saved) {
    messages.value = JSON.parse(saved).map((msg) => ({
      ...msg,
      timestamp: new Date(msg.timestamp),
    }));
  }
};

// Auto-resize textarea
const autoResize = () => {
  nextTick(() => {
    if (messageInput.value) {
      messageInput.value.style.height = "auto";
      messageInput.value.style.height = messageInput.value.scrollHeight + "px";
    }
  });
};

// Watch for message input changes
watch(currentMessage, () => {
  autoResize();
});

// Load messages on mount
onMounted(() => {
  loadMessages();
  scrollToBottom();
});
</script>

<style scoped>
/* Message animations */
.message-enter-active,
.message-leave-active {
  transition: all 0.3s ease;
}

.message-enter-from {
  opacity: 0;
  transform: translateY(20px) scale(0.95);
}

.message-leave-to {
  opacity: 0;
  transform: translateY(-20px) scale(0.95);
}

.message-move {
  transition: transform 0.3s ease;
}

/* Custom scrollbar */
.scrollbar-thin {
  scrollbar-width: thin;
}

.scrollbar-thumb-white\/20::-webkit-scrollbar-thumb {
  background-color: rgba(255, 255, 255, 0.2);
  border-radius: 9999px;
}

.scrollbar-track-transparent::-webkit-scrollbar-track {
  background-color: transparent;
}

.scrollbar-thin::-webkit-scrollbar {
  width: 6px;
}

/* Textarea auto-resize */
textarea {
  min-height: 24px;
  max-height: 120px;
  overflow-y: auto;
  resize: none;
}
</style>
