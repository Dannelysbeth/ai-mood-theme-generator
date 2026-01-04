<template>
  <div class="mood-bg" :style="bgStyle">
    <div class="glass-container">
      <div class="mood-pulse" :style="{ '--mood-glow': currentGlow }"></div>

      <header class="text-center mb-5">
        <h1 class="fw-bold mb-2" :style="{ color: currentColor }">Mood Explorer</h1>
        <p class="text-muted small text-uppercase fw-bold tracking-wider">
          Local AI Reflection (llama3.2)
        </p>
      </header>

      <div class="mb-4">
        <textarea
          v-model="userThought"
          class="form-control form-control-lg border-0 shadow-sm p-4"
          rows="3"
          placeholder="Describe your mood or thought..."
          style="border-radius: 1.5rem; background: rgba(255, 255, 255, 0.6); resize: none"
          @keyup.enter.ctrl="handleGenerate"
        ></textarea>
      </div>

      <div class="d-grid">
        <button
          @click="handleGenerate"
          :disabled="isLoading || !userThought.trim()"
          class="btn btn-generate text-white"
          :style="{ backgroundColor: currentColor, borderColor: currentColor }"
        >
          <span v-if="isLoading" class="spinner-grow spinner-grow-sm me-2"></span>
          {{ isLoading ? 'Thinking...' : 'Generate Insight' }}
        </button>
      </div>

      <transition name="fade-slide" mode="out-in">
        <div v-if="result" :key="result.text" class="output-box shadow-sm">
          <h5 class="fw-bold mb-2" :style="{ color: currentColor }">{{ result.sentiment }}</h5>
          <p class="fs-5 mb-0 text-dark leading-relaxed">
            {{ result.text }}
          </p>
        </div>
        <div v-else-if="isLoading" class="text-center mt-5">
          <p class="loading-text text-muted small uppercase">Accessing local llama3.2...</p>
        </div>
      </transition>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const userThought = ref('')
const isLoading = ref(false)
const result = ref(null)

// Default color values
const currentColor = ref('#0dcaf0')
const currentGlow = ref('rgba(13, 202, 240, 0.3)')

// Ollama Configuration
const ollamaUrl = 'http://localhost:11434/api/generate'
const modelName = 'llama3.2'

const bgStyle = computed(() => ({
  backgroundColor: result.value ? 'transparent' : '#f8f9fa',
}))

const handleGenerate = async () => {
  if (!userThought.value.trim() || isLoading.value) return

  isLoading.value = true
  result.value = null

  try {
    const prompt = `
      You are a mood-sensing AI. Analyze the user's thought and return ONLY a valid JSON object.
      User thought: "${userThought.value}"
      
      The JSON must contain:
      {
        "text": "A poetic, short response (1-2 sentences) to their thought",
        "sentiment": "A single-word name for the mood",
        "hex_color": "A valid CSS hex color that matches the mood",
        "glow_color": "Same color as hex_color but in rgba format with 0.3 opacity"
      }
    `

    const response = await fetch(ollamaUrl, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        model: modelName,
        prompt: prompt,
        stream: false, // Set to false as requested
        format: 'json', // Ensuring structured output
      }),
    })

    if (!response.ok) throw new Error(`Ollama Error: ${response.status}`)

    const data = await response.json()

    // Ollama returns the generated text in the 'response' field
    const parsed = JSON.parse(data.response)

    currentColor.value = parsed.hex_color || '#0dcaf0'
    currentGlow.value = parsed.glow_color || 'rgba(13, 202, 240, 0.3)'
    result.value = parsed
  } catch (error) {
    console.error(error)
    result.value = {
      text: 'Couldn\'t reach llama3.2. Ensure Ollama is running with CORS enabled (OLLAMA_ORIGINS="*").',
      sentiment: 'Disconnected',
      hex_color: '#6c757d',
      glow_color: 'rgba(108, 117, 125, 0.3)',
    }
  } finally {
    isLoading.value = false
  }
}
</script>
