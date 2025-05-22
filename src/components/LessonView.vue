<template>
    <!-- Render the converted HTML content of the lesson -->
    <div v-html="contentHtml" class="lesson-content"></div>
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
  const contentHtml = ref('<p>Loading lesson...</p>');
  
  // Function to load and render a lesson markdown file
  async function loadLesson(num) {
    const url = `lessons/lesson${num}.md`;  // file path in public folder
    try {
      const res = await fetch(url);
      if (!res.ok) {
        // If file not found or error, show an error message
        contentHtml.value = `<p>Error: Lesson ${num} content not found.</p>`;
        return;
      }
      const markdownText = await res.text();
      // Convert Markdown to HTML
      contentHtml.value = mdParser.render(markdownText);
    } catch (e) {
      console.error('Failed to load lesson', e);
      contentHtml.value = `<p>Error loading lesson ${num}.</p>`;
    }
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
  