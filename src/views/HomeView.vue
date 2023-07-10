<script setup lang="ts">
  import axios from 'axios';
  import { TEST_URI } from '@/types/Config';
  import { onMounted, ref } from 'vue';

  const urls = ref<String[] | undefined>();
  const url = ref("");
  onMounted( () => {
    axios.get<{urls: String[]}>('http://localhost:3000/scrape', {
      params: { webUri: TEST_URI } 
    }).then((response) => {
      urls.value = response.data.urls;
    });
  });
</script>
<template>
  <main>
    <v-text-field label="URL" v-model="url"></v-text-field>
    <div v-for="url in urls">
      {{ url }}
    </div>
  </main>
</template>

<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
</style>
