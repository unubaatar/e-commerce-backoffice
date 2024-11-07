<template>
  <div>
    <div style="font-size: 28px; font-weight: 400">Бүтээгдхүүн дэлгэрэнгүй</div>
    <v-card class="pa-8 mt-6" rounded="lg" elevation="0">
      <v-row>
        <v-col cols="2" class="d-flex justify-center align-center">
          <v-btn
            variant="outlined"
            class="w-75"
            style="height: 75%; font-size: 24px"
            rounded="xl"
            elevation="0"
            color="primary"
            icon="mdi-plus"
          ></v-btn
        ></v-col>
        <v-col cols="2" v-for="image in product.thumbnails">
          <img
            class="w-100"
            style="object-fit: fill; aspect-ratio: 1"
            :src="image"
            alt=""
          />
        </v-col>
      </v-row>

      <v-row>
        <v-col cols="4">
          <v-text-field
            v-model="product.name"
            hide-details
            label="Нэр"
            variant="outlined"
          ></v-text-field>
        </v-col>
        <v-col cols="4">
          <v-text-field
            v-model="product.price"
            hide-details
            label="Үнэ"
            variant="outlined"
            type="number"
          ></v-text-field
        ></v-col>
        <v-col cols="4">
          <v-text-field
            v-model="product.sellPrice"
            hide-details
            label="Хямдарсан үнэ"
            variant="outlined"
            type="number"
          ></v-text-field
        ></v-col>
        <v-col cols="4">
          <v-select
            v-model="product.brand"
            :items="brands"
            item-title="name"
            item-value="_id"
            hide-details
            label="Бренд"
            variant="outlined"
          ></v-select>
        </v-col>
        <v-col cols="4">
          <v-select
            v-model="product.taxon"
            :items="catogories"
            item-title="name"
            item-value="_id"
            hide-details
            label="Ангилал"
            variant="outlined"
          ></v-select
        ></v-col>
      </v-row>
      <div class="mt-8 d-flex w-100 justify-end">
        <v-btn @click="updateProduct()" variant="outlined" color="green"
          >Бүтээгдхүүн засах</v-btn
        >
      </div>
    </v-card>
  </div>
</template>

<script setup lang="ts">
definePageMeta({
  layout: "layout",
  middleware: ['auth']
});

import axios from "axios";
import { ref, onMounted } from "vue";
import { useRoute, useRouter } from "vue-router";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";
import moment from "moment";

const config = useRuntimeConfig();
const baseURL = config.public.baseURL;
const router = useRouter();
const route = useRoute();

const product = ref<any>({});
const brands = ref<any>([]);
const catogories = ref<any>([]);

const updateProduct = async () => {
  try {
    const response = await axios.post(`${baseURL}/products/update`, product.value);
    if(response.status === 200) {
        toast.success("Амжилттай");
        await getProduct();
    } else {
        toast.error("Алдаа заалаа");
    }
  } catch (err) {
    console.log(err);
  }
};

const fetchBrands = async () => {
  try {
    const query = {
      isActive: true,
    };
    const response = await axios.post(`${baseURL}/brands/list`, query);
    if (response.status === 200) {
      brands.value = response.data.rows;
    } else {
      console.log("jiijii");
    }
  } catch (err) {
    console.log(err);
  }
};

const fetchCatogeries = async () => {
  try {
    const query = {
      isActive: true,
    };
    const response = await axios.post(`${baseURL}/categories/list`, query);
    if (response.status === 200) {
      catogories.value = response.data.rows;
    } else {
      console.log("jiijii");
    }
  } catch (err) {
    console.log(err);
  }
};

const getProduct = async () => {
  try {
    const query = {
      _id: route.params.id,
    };
    const response = await axios.post(`${baseURL}/products/getById`, query);
    if (response.status === 200) {
      product.value = response.data;
    } else {
      console.log("jiijii");
    }
  } catch (err) {
    console.log(err);
  }
};

onMounted(async () => {
  await getProduct();
  await fetchBrands();
  await fetchCatogeries();
});
</script>
