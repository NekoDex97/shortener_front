<template>
  <div class="min-h-screen bg-gradient-to-br from-purple-100 to-indigo-200 flex items-center justify-center p-4">
    <div class="w-full max-w-md bg-white rounded-xl shadow-xl overflow-hidden">
      <div class="p-8">
        <h1 class="text-3xl font-bold text-gray-800 mb-6 text-center">URL Shortener</h1>
        
        <form @submit.prevent="shortenUrl" class="mb-6">
          <div class="relative">
            <input
              v-model="longUrl"
              type="url"
              placeholder="Enter your long URL here"
              required
              class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-purple-600 focus:border-transparent"
              :class="{ 'border-red-500': error }"
            />
            <button
              type="submit"
              :disabled="isLoading || !longUrl"
              class="absolute right-2 top-2 bg-purple-600 text-white rounded-lg px-4 py-2 font-medium hover:bg-purple-700 focus:outline-none focus:ring-2 focus:ring-purple-600 focus:ring-opacity-50 transition duration-300 ease-in-out disabled:opacity-50 disabled:cursor-not-allowed"
            >
              <span v-if="!isLoading">Shorten</span>
              <LinkIcon v-else class="animate-spin h-5 w-5" />
            </button>
          </div>
          <p v-if="error" class="mt-2 text-red-600 text-sm">{{ error }}</p>
        </form>
        
        <transition name="fade">
          <div v-if="shortUrl" class="bg-gray-100 rounded-lg p-4">
            <p class="text-sm text-gray-600 mb-2">Your shortened URL:</p>
            <div class="flex items-center justify-between bg-white border border-gray-300 rounded-lg p-2">
              <a :href="shortUrl" target="_blank" class="text-purple-600 font-medium truncate">{{ shortUrl }}</a>
              <button
                @click="copyToClipboard"
                class="ml-2 text-gray-500 hover:text-gray-700 focus:outline-none transition duration-150 ease-in-out"
              >
                <CopyIcon class="h-5 w-5" />
              </button>
            </div>
            <p v-if="copied" class="mt-2 text-green-600 text-sm">Copied to clipboard!</p>
          </div>
        </transition>
      </div>
    </div>
  </div>
  </template>
  
  <script setup>
  import { ref } from 'vue'
  import { LinkIcon, CopyIcon } from 'lucide-vue-next'
  
  const longUrl = ref('')
  const shortUrl = ref('')
  const isLoading = ref(false)
  const error = ref('')
  const copied = ref(false)
  
  const shortenUrl = async () => {
    error.value = ''
    if (!isValidUrl(longUrl.value)) {
      error.value = 'Please enter a valid URL'
      return
    }
    
    isLoading.value = true
    const formData = new FormData();
    formData.append('url', longUrl.value);
    const response = await fetch('https://url-shortener-1-qbrq.onrender.com/shorten/',
    {method: 'POST',
      body: formData, // Envía el valor del input
    });
    const data = await response.json();
    console.log(data)
    shortUrl.value = ''
    //API call here to get the shortened URL
    shortUrl.value = data.short_url;
    isLoading.value = false
  }
  
  const isValidUrl = (url) => {
    try {
      new URL(url)
      return true
    } catch {
      return false
    }
  }
  
  const copyToClipboard = () => {
    navigator.clipboard.writeText(shortUrl.value)
    copied.value = true
    setTimeout(() => {
      copied.value = false
    }, 2000)
  }
  </script>
  
  <style scoped>
  .fade-enter-active,
  .fade-leave-active {
    transition: opacity 0.5s ease;
  }
  
  .fade-enter-from,
  .fade-leave-to {
    opacity: 0;
  }
  </style>