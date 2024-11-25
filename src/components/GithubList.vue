<template>
  <h1>GitHub Repositories</h1>
  <div v-if="loading">Loading...</div>
  <div v-if="error" class="error">{{ error }}</div>

  <div v-if="repositories.length">
    <div v-for="repo in repositories" :key="repo.id">
      <Item>
        <template #icon>
          <ToolingIcon />
        </template>
        <template #heading><a :href="repo.html_url" target="_blank">{{ repo.name }}</a></template>
        {{ repo.description }}
      </Item>
    </div>
  </div>

  <div v-if="!loading && repositories.length === 0">No repositories found.</div>
</template>

<script setup>
import Item from "./Item.vue";
import SupportIcon from "./icons/IconSupport.vue";
import ToolingIcon from "./icons/IconTooling.vue";
import { ref, onMounted } from "vue";

// Props
defineProps({
  username: {
    type: String,
    required: true,
  },
});

// State
const repositories = ref([]);
const loading = ref(true);
const error = ref(null);

// Fetch function
const fetchRepositories = async () => {
  loading.value = true;
  error.value = null;
  try {
    const response = await fetch(`https://api.github.com/users/austin-shaw/repos`);
    if (!response.ok) {
      throw new Error("Failed to fetch repositories");
    }
    repositories.value = await response.json();
  } catch (err) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
};

// Lifecycle hook
onMounted(fetchRepositories);
</script>

<style>
.error {
  color: red;
  font-weight: bold;
}
</style>