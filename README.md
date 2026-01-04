#🌌 AI Mood Explorer

AI Mood Explorer is a private, local-first web application that analyzes your thoughts and reflections to generate poetic insights. Unlike traditional AI apps, this tool uses a local Large Language Model (LLM) to ensure your data stays on your machine, while dynamically transforming the UI's visual atmosphere to match your emotional state.

## ✨ Features

Local Inference: Powered by Ollama and llama3.2 for 100% private processing.

Dynamic Emotional Theming: The UI colors, glow effects, and headers automatically shift based on the AI's sentiment analysis.

Glassmorphism Design: A modern, high-end aesthetic with frosted glass effects and smooth floating animations.

Vibration Pulse: A rhythmic glowing aura that reacts to the "vibe" of your input.

Structured JSON Output: Uses a strictly defined prompt to ensure consistent UI updates from the local model.

## 🚀 Getting Started

### 1. Prerequisites

Node.js: Version 16.x or higher.

Ollama: Download and install from ollama.com.

### 2. Pull the Model

Open your terminal and pull the optimized llama3.2 model:

ollama pull llama3.2


### 3. Critical: Enable Browser Access (CORS)

By default, Ollama blocks requests from web browsers for security. You must set the OLLAMA_ORIGINS environment variable to allow the frontend to communicate with the local API.

macOS / Linux:

OLLAMA_ORIGINS="*" ollama serve


Windows (PowerShell):

$env:OLLAMA_ORIGINS="*"; ollama serve


## 📦 Installation

Clone the project or copy the files provided in the vue_project_structure.md.

Navigate to the project directory.

Install dependencies:

npm install


Run the Development Server:

npm run dev


## 🛠️ Configuration

You can customize the model or the endpoint in src/App.vue:

// Ollama Configuration
const ollamaUrl = "http://localhost:11434/api/generate";
const modelName = "llama3.2"; // Change to "mistral", "phi3", etc., if pulled locally


## 📝 Usage

Type a thought, a single keyword, or a complex mood into the text area.

Press Generate Insight (or Ctrl + Enter).

Watch as the "Vibration Pulse" changes color and the AI types out a poetic reflection on your state of mind.
