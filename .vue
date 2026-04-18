<script setup>
import { ref, onMounted } from 'vue'

const extensions = ref([])

// Fetch all installed extensions on load
const fetchExtensions = () => {
  if (typeof chrome !== 'undefined' && chrome.management) {
    chrome.management.getAll((info) => {
      // Filter out this manager itself to avoid accidental self-disabling
      extensions.value = info.filter(ext => ext.id !== chrome.runtime.id)
    })
  } else {
    console.warn("Chrome Management API not found. Are you running this as an extension?")
  }
}

// Toggle an extension's enabled state
const toggleExtension = (ext) => {
  chrome.management.setEnabled(ext.id, !ext.enabled, () => {
    ext.enabled = !ext.enabled // Update local UI state
  })
}

onMounted(fetchExtensions)
</script>

<template>
  <div class="manager">
    <h3>Manage Extensions</h3>
    <ul>
      <li v-for="ext in extensions" :key="ext.id" class="ext-item">
        <span>{{ ext.name }}</span>
        <button @click="toggleExtension(ext)">
          {{ ext.enabled ? 'Disable' : 'Enable' }}
        </button>
      </li>
    </ul>
  </div>
</template>

<style scoped>
.manager { width: 300px; padding: 10px; font-family: sans-serif; }
.ext-item { 
  display: flex; 
  justify-content: space-between; 
  margin-bottom: 8px; 
  padding-bottom: 4px; 
  border-bottom: 1px solid #eee;
}
button { cursor: pointer; }
</style>
