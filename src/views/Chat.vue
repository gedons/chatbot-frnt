<template>
    <div class="bg-gray-50">
      <!-- Navigation Bar -->
      <nav class="bg-gray-900 p-4">
        <div class="max-w-7xl mx-auto px-2 sm:px-6 lg:px-8">
          <div class="relative flex items-center justify-between h-16">
            <div class="absolute inset-y-0 left-0 flex items-center sm:hidden">
              <!-- Mobile menu button -->
              <button @click="toggleMobileMenu" class="inline-flex items-center justify-center p-2 rounded-md text-white focus:outline-none focus:bg-gray-600 transition duration-150 ease-in-out" aria-label="Main menu" aria-expanded="false">
                <!-- Icon when menu is closed. -->
                <svg class="h-6 w-6" stroke="currentColor" fill="none" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16"></path>
                </svg>
                <!-- Icon when menu is open. -->
                <svg v-show="isMobileMenuOpen" class="h-6 w-6" stroke="currentColor" fill="none" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                </svg>
              </button>
            </div>
            <div class="flex-1 flex items-center justify-center sm:items-stretch sm:justify-start">
              <div class="flex-shrink-0">
                <h1 class="text-white text-lg font-semibold">Chat App</h1>
              </div>
              <div class="hidden sm:block sm:ml-6">
                <div class="flex">
                    <router-link 
                    :to="{ name: 'Home' }"
                    class="text-white text-sm underline mt-1  font-semibold"
                  >
                    Back Home
                  </router-link>
                </div>
              </div>
            </div>
          </div>
        </div>
  
        <!-- Mobile menu, toggle classes based on menu state. -->
        <div v-show="isMobileMenuOpen" class="sm:hidden">
          <!-- Mobile menu items -->
          <router-link 
          :to="{ name: 'Home' }"
          class="text-white text-sm underline ml-2  font-semibold"
        >
          Back Home
        </router-link>
        </div>
      </nav>

    <!-- Welcome Card -->
    <div class="max-w-xl mx-auto mt-6 p-6 bg-white rounded-lg shadow-md text-center">
        <p>Welcome to our smartphone store! We have brands like
            <br><span class="font-semibold">Apple, Samsung, Andriod, OnePlus.</span> 
            <br> Which brand are you interested in?</p>
      </div>
  
      <!-- Chat Container -->
      <div class="chat-container mt-8 max-w-3xl mx-auto p-4" ref="messageContainer">
            <!-- Chat messages display -->
            <div class="chat-messages">
            <div v-for="message in messages" :key="message.id" :class="messageClass(message)">
                <p v-html="formatMessage(message.text)"></p>
            </div>
            <!-- Typing indicator -->
            <div v-if="isTyping" class="message-bot">
                <p>Bot is typing...</p>
            </div>
           <!-- Lottie animation display -->
           <div v-if="showLottieAnimation"  class="fullscreen-container">
            <Vue3Lottie                 
                :autoplay="true"
                :loop="true" 
                :animationData="successAnimation"               
            />
            </div>
        </div>
  
        <!-- Message input form -->
        <form @submit.prevent="sendMessage" class="flex mt-4">
          <input v-model="inputMessage" type="text" placeholder="Type your message..." class="flex-1 px-4 py-2 rounded-l-lg border border-gray-300 focus:outline-none focus:border-gray-500" />
          <button type="submit" :disabled="chatEnded" class="bg-gray-900 hover:bg-gray-800 text-white px-4 py-2 rounded-r-lg">Send</button>
          <button v-if="hasMessageSent" @click="endChat" type="button" class="bg-red-500 hover:bg-red-600 text-white px-4 py-2 rounded-lg ml-2">End Chat</button>
        </form>
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  import { Vue3Lottie } from 'vue3-lottie';
  import notificationSoundFile from '../assets/noti.mp3';
  import successAnimation from '../assets/success.json';
  
  export default {
    components: {
        Vue3Lottie
    },
    data() {
      return {
        messages: [],
        inputMessage: '',
        isMobileMenuOpen: false,
        chatEnded: false,
        hasMessageSent: false,
        notificationSound: new Audio(notificationSoundFile) ,
        showLottieAnimation: false,
        successAnimation,
        isTyping: false,
      };
    },
    methods: {
      async sendMessage() {
        if (this.chatEnded) return;

        try {
            this.messages.push({ id: this.messages.length + 1, text: 'User: ' + this.inputMessage });
            const userMessage = this.inputMessage;
            this.inputMessage = '';
            this.isTyping = true;  
            this.scrollToBottom(); 

            setTimeout(async () => {
            const response = await axios.post('https://chat-bot-ad8c.onrender.com/chat', {
                message: userMessage
            });
            const botResponse = response.data.response;
            this.messages.push({ id: this.messages.length + 2, text: 'Bot: ' + botResponse });
            this.playNotificationSound();
            this.isTyping = false;  
            this.hasMessageSent = true;
            this.scrollToBottom(); 

            // Check for the specific response messages to display the Lottie animation
            if (botResponse.includes("Congratulations! You have successfully made payment with your card.") ||
                botResponse.includes("Congratulations! You have successfully made payment via transfer")) {
                this.showLottieAnimation = true;
            } else {
                this.showLottieAnimation = false;
            }
            }, 1000);  
        } catch (error) {
            console.error('Error sending message:', error);
        }
      },
      formatMessage(message) {
        const formattedMessage = message.replace(/\n/g, '<br>');
        // Convert image URLs to HTML img tags
        return formattedMessage.replace(/(http[s]?:\/\/[^\s]+\.(?:jpg|png|gif))/g, '<img src="$1" alt="Image" style="max-width: 100%; height: 400px;" onerror="this.onerror=null;this.src=\'https://via.placeholder.com/150\';" />');
      },
      messageClass(message) {
        return message.text.startsWith('Bot:') ? 'message-bot' : 'message-user';
      },
      endChat() {
        this.messages.push({ id: this.messages.length + 1, text: 'User: End Chat' });
        this.messages.push({ id: this.messages.length + 2, text: 'Bot: The chat has ended. Thank you!' });
        this.chatEnded = true; 
      },
      playNotificationSound() {
        this.notificationSound.play();
      },
      scrollToBottom() {
        this.$nextTick(() => {
        const messageContainer = this.$refs.messageContainer;
        if (messageContainer) {
          messageContainer.scrollTop = messageContainer.scrollHeight;
        }
      });
      },
      toggleMobileMenu() {
        this.isMobileMenuOpen = !this.isMobileMenuOpen;
      }
    }
  };
  </script>
  
<style scoped>
  .message-bot {
    text-align: left;
    background-color:rgb(248, 235, 235);
    padding: 10px;
    margin-bottom: 10px;
    border-radius: 8px;
  }
  
  .message-user {
    text-align: left;
    background-color: #111827;
    color: white;
    padding: 10px;
    margin-bottom: 10px;
    border-radius: 8px;
  }
  button:disabled {
    background-color: #ccc;
    cursor: not-allowed;
  }
  .fullscreen-container {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 50%;
    z-index: 9999;    
  }
  
  .fullscreen-lottie {
    width: 100%;
    height: 100%;
  }
</style>