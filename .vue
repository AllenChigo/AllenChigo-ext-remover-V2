<script setup>
import { ref, onMounted } from 'vue'

// Reactive list of extensions
const extensions = ref([])

// Fetch all installed extensions
const getExtensions = () => {
  if (window.chrome && chrome.management) {
    chrome.management.getAll((info) => {
      // Filter out this specific manager to avoid self-disabling
      extensions.value = info.filter(ext => ext.id !== chrome.runtime.id)
    })
  }
}

// Function to enable or disable an extension
const toggle = (ext) => {
  chrome.management.setEnabled(ext.id, !ext.enabled, () => {
    ext.enabled = !ext.enabled // Update the UI state
  })
}

onMounted(getExtensions)
</script>

<template>
  <div class="manager">
    <h2>Extension Manager</h2>
    <ul>
      <li v-for="ext in extensions" :key="ext.id" class="item">
        <span>{{ ext.name }}</span>
        <button @click="toggle(ext)">
          {{ ext.enabled ? 'Disable' : 'Enable' }}
        </button>
      </li>
    </ul>
  </div>
</template>

<style scoped>
.manager { padding: 1rem; width: 250px; }
.item { display: flex; justify-content: space-between; margin-bottom: 5px; }
</style>
