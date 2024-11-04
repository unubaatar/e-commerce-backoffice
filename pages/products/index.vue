<template>
  <div>
    <div>{{ products }}</div>
  </div>
</template>

<script setup lang="ts">
definePageMeta({
  layout: "layout",
});

const config = useRuntimeConfig();
const baseURL = config.public.baseURL;

import axios from "axios";
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";
import moment from "moment";

const router = useRouter();

const products = ref<any>([]);
const productCount = ref<any>(0);

const fetchProducts = async () => {
  try {
    const response = await axios.post(`${baseURL}/products/list`, {});
    if (response.status === 200) {
      products.value = response.data.rows;
      productCount.value = response.data.count;
    } else {
      console.log("jiijii");
    }
  } catch (err) {
    console.log(err);
  }
};


onMounted(async () => {
  await fetchProducts();
});
</script>
