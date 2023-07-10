<script setup lang="ts">
  import axios from 'axios';
  import { TEST_URI } from '@/types/Config';
  import { onMounted } from 'vue';

  let urls: String[] | null;
  onMounted( () => {
    axios.get<{urls: String[]}>('http://localhost:3000/scrape', {
      params: { webUri: TEST_URI } 
    }).then((response) => {
      urls = response.data.urls;
    });
  });
</script>
<template>
  <div class="about">
    <h1>This is an about page</h1>
  </div>
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
