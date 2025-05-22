<template>
  <div class="app">
    <!-- Sidebar -->
    <div class="sidebar" :class="{ active: sidebarActive }">
      <div class="sidebar-header">
        <h1>Environmental Lessons</h1>
        
        <!-- Status bar showing lesson number and streak -->
        <div class="status-bar">
          <span><i class="fas fa-book-open"></i> Lesson {{ displayLessonNumber }} of {{ totalLessons }}</span>
          <span class="streak">{{ streak }} day<span v-if="streak !== 1">s</span></span>
        </div>
      </div>

      <!-- Navigation between lessons -->
      <div class="nav-buttons">
        <div class="nav-label">Lesson Navigation</div>
        <button @click="prevLesson" :disabled="viewLessonNumber <= 1" class="nav-button secondary-button">
          <i class="fas fa-chevron-left"></i> Previous
        </button>
        <button @click="nextLesson" :disabled="viewLessonNumber >= totalLessons" class="nav-button secondary-button">
          Next <i class="fas fa-chevron-right"></i>
        </button>
      </div>
    </div>

    <!-- Mobile sidebar toggle -->
    <button @click="toggleSidebar" class="sidebar-toggle">
      <i class="fas fa-bars"></i>
    </button>

    <!-- Main Content Area -->
    <div class="content">
      <div class="content-header">
        <h1>Daily Environmental Lessons</h1>
      </div>

      <!-- If all lessons are finished -->
      <div v-if="finishedAll" class="message">
        <p><i class="fas fa-trophy"></i> Congratulations! You have completed all {{ totalLessons }} lessons!</p>
        <p>Your final streak: <strong>{{ streak }} days</strong>.</p>
      </div>

      <!-- If user has completed today's lesson and must wait -->
      <div v-else-if="doneForToday" class="message">
        <p><i class="fas fa-check-circle"></i> You have completed Lesson {{ progress.lastLessonCompleted }} for today.</p>
        <p><em>Come back tomorrow for Lesson {{ progress.lastLessonCompleted + 1 }}!</em></p>
        <button @click="continueLesson" class="continue-button primary-button">
          Continue to Next Lesson <i class="fas fa-forward"></i>
        </button>
      </div>

      <!-- Otherwise, show the current lesson content and the completion button -->
      <div v-else>
        <!-- Lesson content viewer -->
        <LessonView :lesson-number="viewLessonNumber" />

        <!-- "Mark Complete" button to finish the lesson -->
        <button @click="markComplete" class="complete-button">
          Mark Complete <i class="fas fa-check"></i>
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { reactive, computed, ref, watch, onMounted } from 'vue';
import LessonView from './components/LessonView.vue';

// Total number of lessons in the curriculum
const totalLessons = 22;

// Reactive state for sidebar toggle on mobile
const sidebarActive = ref(false);

// Toggle sidebar on mobile
function toggleSidebar() {
  sidebarActive.value = !sidebarActive.value;
}

// Reactive state object for user progress
const progress = reactive({
  lastLessonCompleted: 0,     // number of last completed lesson (0 if none)
  lastCompletedDate: null,    // date string "YYYY-MM-DD" of last completion
  streak: 0                   // current streak count of consecutive days
});

// Utility: format a Date as "YYYY-MM-DD" (using local time)
function formatDate(dateObj) {
  const y = dateObj.getFullYear();
  const m = String(dateObj.getMonth() + 1).padStart(2, '0');
  const d = String(dateObj.getDate()).padStart(2, '0');
  return `${y}-${m}-${d}`;
}

// On mounted, load progress from localStorage (if available) and determine today's status
onMounted(() => {
  const storedLast = localStorage.getItem('lastLessonCompleted');
  const storedDate = localStorage.getItem('lastCompletedDate');
  const storedStreak = localStorage.getItem('streak');
  if (storedLast) progress.lastLessonCompleted = Number(storedLast);
  if (storedDate) progress.lastCompletedDate = storedDate;
  if (storedStreak) progress.streak = Number(storedStreak);

  const todayStr = formatDate(new Date());
  if (progress.lastCompletedDate) {
    if (progress.lastCompletedDate === todayStr) {
      // Already completed a lesson today
      // (streak and lastLessonCompleted are already up to date)
    } else {
      // Not completed today yet
      // Check if last completion was yesterday or earlier
      const yesterdayStr = formatDate(new Date(Date.now() - 86400000)); // yesterday
      if (progress.lastCompletedDate === yesterdayStr) {
        // Last lesson was yesterday, streak continues (do nothing yet, will increment on completion)
      } else {
        // Last lesson was before yesterday (or streak broken due to gap)
        progress.streak = 0; // reset current streak due to break
        localStorage.setItem('streak', '0');
      }
    }
  } else {
    // No lastCompletedDate means user is new (no lessons done yet)
    progress.lastLessonCompleted = 0;
    progress.streak = 0;
  }
});

// Compute if all lessons are completed
const finishedAll = computed(() => progress.lastLessonCompleted >= totalLessons);

// Compute if the user has already done today's lesson
const todayStr = formatDate(new Date());
const doneForToday = computed(() => progress.lastCompletedDate === todayStr && !finishedAll.value);

// Compute the current lesson index to display (next lesson if available)
const currentLessonIndex = computed(() => {
  if (finishedAll.value) {
    return progress.lastLessonCompleted; // no "next" lesson, use last completed (22)
  }
  // If not finished, current lesson is last completed + 1
  return progress.lastLessonCompleted + 1;
});

// For display, use current lesson unless it's locked until tomorrow
const displayLessonNumber = computed(() => {
  if (doneForToday.value || finishedAll.value) {
    // show the last completed lesson number (since next is not accessible yet or we're done)
    return progress.lastLessonCompleted;
  } else {
    // show the lesson number currently in progress
    return currentLessonIndex.value;
  }
});

// Add viewLessonNumber state for skipping functionality
const viewLessonNumber = ref(currentLessonIndex.value);

// Reset view when currentLessonIndex changes (e.g., on mount or after completion)
watch(currentLessonIndex, (newVal) => {
  viewLessonNumber.value = newVal;
});

// Method to mark the current lesson as complete
function markComplete() {
  const today = new Date();
  const todayStr = formatDate(today);
  if (progress.lastCompletedDate === todayStr) {
    // Already completed today (shouldn't happen because button would be hidden)
    return;
  }
  // Increment the completed lesson count
  progress.lastLessonCompleted += 1;
  // Update streak count
  if (progress.lastCompletedDate === formatDate(new Date(Date.now() - 86400000))) {
    // Last completed was yesterday, continue streak
    progress.streak += 1;
  } else {
    // Otherwise, start a new streak at 1 (includes case of first lesson or a break in streak)
    progress.streak = 1;
  }
  // Update last completed date to today
  progress.lastCompletedDate = todayStr;

  // Save updated progress to localStorage
  localStorage.setItem('lastLessonCompleted', String(progress.lastLessonCompleted));
  localStorage.setItem('lastCompletedDate', progress.lastCompletedDate);
  localStorage.setItem('streak', String(progress.streak));

  // After marking complete, the next lesson becomes locked until tomorrow.
  // (UI will react to doneForToday computed becoming true since lastCompletedDate == todayStr now)
}

// Method to bypass daily limit and load the next lesson immediately
function continueLesson() {
  const yesterday = new Date(Date.now() - 86400000);
  const yesterdayStr = formatDate(yesterday);
  progress.lastCompletedDate = yesterdayStr;
  localStorage.setItem('lastCompletedDate', yesterdayStr);
}

// Free navigation functions
function prevLesson() {
  if (viewLessonNumber.value > 1) {
    viewLessonNumber.value -= 1;
  }
}

function nextLesson() {
  if (viewLessonNumber.value < totalLessons) {
    viewLessonNumber.value += 1;
  }
}
</script>
