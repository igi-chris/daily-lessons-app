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
  <div v-else v-html="contentHtml" class="lesson-content"></div>
</template>
  
<script setup>
import { ref, onMounted, watch } from 'vue';
import MarkdownIt from 'markdown-it';
  
// Create a Markdown-It parser instance (no plugins for now, just base Markdown)
const mdParser = new MarkdownIt();
  
const props = defineProps({
  lessonNumber: {
    type: Number,
    required: true
  }
});
  
// Reactive state for the lesson content HTML
const contentHtml = ref('');
const isLoading = ref(true);
const hasError = ref(false);
const errorMessage = ref('');
  
// Function to load and render a lesson markdown file
async function loadLesson(num) {
  isLoading.value = true;
  hasError.value = false;
  
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

// Function to retry loading when error occurs
function retryLoading() {
  loadLesson(props.lessonNumber);
}
  
// Load the lesson when component is mounted
onMounted(() => {
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
</style>
  