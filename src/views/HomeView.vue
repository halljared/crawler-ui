<script setup lang="ts">
  import axios from 'axios';
  import { TEST_URI } from '@/types/Config';
  import { onMounted, ref, shallowRef } from 'vue';
  import WebVisualization from '@/components/WebVisualization.vue';
  import type { WebNode } from '@/types/WebNode';

  const urls = ref<string[] | undefined>();
  const url = ref("");
  let webNode = shallowRef<WebNode | undefined>();
  onMounted( () => {
    axios.get<{urls: string[]}>('http://localhost:3000/scrape', {
      params: { webUri: TEST_URI } 
    }).then((response) => {
      urls.value = response.data.urls;
      webNode.value = {
        url: TEST_URI,
        children: urls.value.map((item) => {
          return {
            url: item,
            children: urls.value?.map((item) => {
              return {
                url: item
              }
            })
          }
        })
      }
    });
  });
</script>
<template>
  <v-container style="height:100%;">
    <v-row class="flex-column" style="height:100%;">
      <v-col class="flex-grow-0">
        <v-row>
          <v-col cols="8">
            <v-text-field label="URL" v-model="url"></v-text-field>
          </v-col>
          <v-col class="d-flex align-center justify-center">
            <v-btn>Go</v-btn>
          </v-col>
        </v-row>
        <!-- <v-row v-for="url in urls">
          {{ url }}
        </v-row> -->
      </v-col>
      <v-col class="flex-grow-1 canvas">
        <web-visualization v-if="webNode" :web-node="webNode" />
      </v-col>
    </v-row>
  </v-container>
</template>

<style scoped>
  .canvas {
    background-color: #403c44;
    padding: 0px;
  }
  /* .header {
    flex: 0 1 auto;
  }
  .main {
    flex: 1 1 auto;
  } */
</style>
