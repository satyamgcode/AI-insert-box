<template>
    <div class="mt-10 m-48 border border-gray-500 p-14">
      <p @mouseup="handleTextSelection">
        Time zones are regions of the world divided based on the local time in relation to Coordinated Universal Time (UTC). They help standardize time across different locations on the planet. Each time zone is usually one hour ahead or behind neighboring zones, adjusting for the Earth's rotation. There are 24 standard time zones, but some regions may use half-hour or even 15-minute offsets. Time zones can also shift seasonally due to daylight saving time, where clocks are set forward or backward to make better use of daylight.
      </p>
      <button
      class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-0 px-2 rounded"
        v-if="showEditButton"
        :style="{ position: 'absolute', top: `${buttonPosition.y}px`, left: `${buttonPosition.x}px` }"
        @click="editText"
      >
        Edit
      </button>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted, onBeforeUnmount } from 'vue';
  
  const showEditButton = ref(false);
  const buttonPosition = ref({ x: 0, y: 0 });
  const selectedText = ref('');
  
  const handleTextSelection = () => {
    const selection = window.getSelection();
    if (selection && selection.toString().length > 0) {
      const range = selection.getRangeAt(0);
      const rect = range.getBoundingClientRect();
      buttonPosition.value = { x: rect.left + window.scrollX, y: rect.top + window.scrollY - 30 };
      showEditButton.value = true;
      selectedText.value = selection.toString();
    }
  };
  
  const handleSelectionChange = () => {
    const selection = window.getSelection();
    if (!selection || selection.toString().length === 0) {
      showEditButton.value = false;
      selectedText.value = '';
    }
  };
  
  onMounted(() => {
    document.addEventListener('selectionchange', handleSelectionChange);
  });
  
  onBeforeUnmount(() => {
    document.removeEventListener('selectionchange', handleSelectionChange);
  });
  
  const editText = () => {
  if (selectedText.value) {
    alert(`Selected text: "${selectedText.value}"`);
    window.getSelection().removeAllRanges(); // Clear the text selection
    showEditButton.value = false; // Hide the edit button
  } else {
    alert('No text selected.');
  }
};

  </script>
  
  <style scoped>
  p {
    user-select: text;
  }
  </style>
  