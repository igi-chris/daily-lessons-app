<template>
  <!-- Show loading spinner while content is loading -->
  <div v-if="isLoading" class="lesson-loading">
    <div class="spinner"></div>
    <p>Loading lesson...</p>
  </div>
  
  <!-- Show error message if loading fails -->
  <div v-else-if="hasError" class="lesson-error">
    <i class="fas fa-exclamation-triangle"></i>
    <p>{{ errorMessage }}</p>
    <button @click="retryLoading" class="retry-button">
      <i class="fas fa-sync-alt"></i> Try Again
    </button>
  </div>
  
  <!-- Render the converted HTML content of the lesson -->
  <div v-else class="lesson-container">
    <!-- AI content disclaimer -->
    <div class="ai-disclaimer">
      <i class="fas fa-robot"></i>
      <p>{{ randomDisclaimer }}</p>
    </div>
    
    <div v-html="contentHtml" class="lesson-content"></div>
  </div>
</template>
  
<script setup>
import { ref, onMounted, watch } from 'vue';
import MarkdownIt from 'markdown-it';
  
// Create a Markdown-It parser instance (no plugins for now, just base Markdown)
const mdParser = new MarkdownIt();

const emit = defineEmits(['titleLoaded']);
  
const props = defineProps({
  lessonNumber: {
    type: Number,
    required: true
  }
});

// Collection of witty disclaimers
const disclaimers = [
  "AI-generated content: 60% facts, 40% confident guesses, 100% enthusiasm!",
  "This content was dreamt up by an AI. Approach with curiosity and a pinch of salt.",
  "AI-crafted content: Like Wikipedia, but with more personality and fewer citations.",
  "Our AI wrote this with digital fingers crossed. Verify before citing!",
  "AI wisdom: Part science, part educated guesses, all delivered with confidence.",
  "Created by an algorithm that's 87% confident about 62% of this content.",
  "Knowledge by AI: Like having a very well-read but slightly confused friend.",
  "Factual content with a sprinkle of AI imagination. Spot the difference!"
];

// Randomly select a disclaimer
const randomDisclaimer = ref('');

function selectRandomDisclaimer() {
  const randomIndex = Math.floor(Math.random() * disclaimers.length);
  randomDisclaimer.value = disclaimers[randomIndex];
}
  
// Reactive state for the lesson content HTML
const contentHtml = ref('');
const isLoading = ref(true);
const hasError = ref(false);
const errorMessage = ref('');
  
// Function to load and render a lesson markdown file
async function loadLesson(num) {
  isLoading.value = true;
  hasError.value = false;
  
  // Select a new random disclaimer when loading a lesson
  selectRandomDisclaimer();
  
  const url = `lessons/lesson${num}.md`;  // file path in public folder
  try {
    const res = await fetch(url);
    if (!res.ok) {
      // If file not found or error, show an error message
      hasError.value = true;
      errorMessage.value = `Lesson ${num} content not found. Please try again later.`;
      return;
    }
    const markdownText = await res.text();
    
    // Extract title from markdown (typically first h1/h2)
    const title = extractTitleFromMarkdown(markdownText, num);
    
    // Emit the title to parent component
    emit('titleLoaded', { lessonNumber: num, title });
    
    // Convert Markdown to HTML
    contentHtml.value = mdParser.render(markdownText);
  } catch (e) {
    console.error('Failed to load lesson', e);
    hasError.value = true;
    errorMessage.value = `Error loading lesson ${num}. Please check your connection and try again.`;
  } finally {
    isLoading.value = false;
  }
}

// Extract title from markdown content
function extractTitleFromMarkdown(markdown, fallbackNumber) {
  // First try to find a heading at the start of the document
  // Look for # or ## patterns typical in markdown
  const headingRegex = /^#+ (.+)$/m;
  const match = markdown.match(headingRegex);
  
  if (match && match[1]) {
    // Return the heading text without any markdown formatting
    return match[1].trim();
  }
  
  // Fallback title if no heading found
  return `Lesson ${fallbackNumber}`;
}

// Function to retry loading when error occurs
function retryLoading() {
  loadLesson(props.lessonNumber);
}
  
// Load the lesson when component is mounted
onMounted(() => {
  // Select an initial random disclaimer
  selectRandomDisclaimer();
  loadLesson(props.lessonNumber);
});
  
// Watch for changes in lessonNumber prop (in case user advances to next lesson in same session)
watch(() => props.lessonNumber, (newLesson) => {
  loadLesson(newLesson);
});
</script>

<style scoped>
.lesson-loading, .lesson-error {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 200px;
  padding: 2rem;
  text-align: center;
}

.lesson-error {
  color: var(--accent-dark);
  background-color: var(--accent-light);
  border-radius: var(--radius-md);
  padding: 2rem;
}

.lesson-error i {
  font-size: 2rem;
  margin-bottom: 1rem;
}

.spinner {
  border: 4px solid rgba(0, 0, 0, 0.1);
  border-radius: 50%;
  border-top: 4px solid var(--primary);
  width: 40px;
  height: 40px;
  margin-bottom: 1rem;
  animation: spin 1s linear infinite;
}

[data-theme="dark"] .spinner {
  border-color: rgba(255, 255, 255, 0.1);
  border-top-color: var(--primary);
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.retry-button {
  margin-top: 1rem;
  padding: 0.5rem 1rem;
  background-color: var(--primary);
  color: white;
  border: none;
  border-radius: 20px;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  gap: 8px;
}

.retry-button:hover {
  background-color: var(--primary-dark);
  transform: translateY(-2px);
}

.ai-disclaimer {
  margin-bottom: 1rem;
  padding: 0.75rem 0.75rem;
  border-radius: var(--radius-md);
  background-color: rgba(0, 0, 0, 0.05);
  display: flex;
  align-items: center;
  gap: 0.75rem;
  font-size: 0.9rem;
  color: var(--text-secondary);
}

[data-theme="dark"] .ai-disclaimer {
  background-color: rgba(255, 255, 255, 0.05);
}

.ai-disclaimer i {
  font-size: 1.1rem;
  color: var(--primary);
}
</style>
  