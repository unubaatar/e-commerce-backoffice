<template>
  <div>
    <div style="font-size: 28px; font-weight: 400">Брендийн жагсаалт</div>

    <div class="d-flex justify-space-between mt-2">
      <v-text-field
        rounded="xl"
        prepend-inner-icon="mdi-magnify"
        density="compact"
        hide-details
        max-width="300"
        label="Бренд хайх"
        variant="outlined"
      ></v-text-field>
      <v-btn rounded="xl" prepend-icon="mdi-plus" color="primary">
        Бренд нэмэх</v-btn
      >
    </div>
    <div>
      {{ brands }}
    </div>
  </div>
</template>

<script setup lang="ts">
definePageMeta({
  layout: "layout",
});

const config = useRuntimeConfig();
const baseURL = config.public.baseURL;

import axios from 'axios';
import { ref , onMounted } from 'vue';

const brands = ref<any>([]);
const brandsCount = ref<any>([]);

const getData = async() => {
  try {
    const query = {
      page: 1,
      per_page : 10
    }
    const response = await axios.post(`
http://localhost:4004/api/brands/list` ,query );
    if(response.status === 200) {
      brands.value = response.data.rows;
      brandsCount.value = response.data.count;
    } else {
      console.log("jiijii");
    }
  } catch(err) {
    console.log(err);
  }
}

onMounted(async () => {
  await getData();
})
</script>
