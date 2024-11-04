<template>
  <div>
    <div style="font-size: 28px; font-weight: 400">Бүтээгдхүүн нэмэх</div>
    <v-card class="pa-8 mt-6" rounded="lg" elevation="0">
      <v-row>
        <v-col cols="8">
          <v-row>
            <v-col cols="6">
              <v-text-field
                v-model="productToAdded.name"
                hide-details
                label="Нэр"
                variant="outlined"
              ></v-text-field>
            </v-col>
            <v-col cols="6">
              <v-text-field
                v-model="productToAdded.thumbnails"
                hide-details
                label="Зураг"
                variant="outlined"
              ></v-text-field
            ></v-col>
          </v-row>

          <v-row>
            <v-col cols="6">
              <v-text-field
                v-model="productToAdded.price"
                hide-details
                label="Үнэ"
                variant="outlined"
                type="number"
              ></v-text-field
            ></v-col>
            <v-col cols="6">
              <v-text-field
                v-model="productToAdded.sellPrice"
                hide-details
                label="Хямдарсан үнэ"
                variant="outlined"
                type="number"
              ></v-text-field
            ></v-col>
          </v-row>

          <v-row>
            <v-col cols="6">
              <v-select
                v-model="productToAdded.brand"
                :items="brands"
                item-title="name"
                item-value="_id"
                hide-details
                label="Бренд"
                variant="outlined"
              ></v-select>
            </v-col>
            <v-col cols="6">
              <v-select
                v-model="productToAdded.taxon"
                :items="catogories"
                item-title="name"
                item-value="_id"
                hide-details
                label="Ангилал"
                variant="outlined"
              ></v-select
            ></v-col> </v-row
        ></v-col>
        <v-col cols="4">
          <img style="width: 80%" :src="productToAdded.thumbnails" alt="" />
        </v-col>
      </v-row>

      <div class="mt-16">
        <v-btn @click="addProduct()" variant="outlined" color="green"
          >Бүтээгдхүүн нэмэх</v-btn
        >
      </div>
    </v-card>
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

const brands = ref<any>([]);
const catogories = ref<any>([]);
const productToAdded = ref<any>({});

const fetchBrands = async () => {
  try {
    const query = {
      //   isActive: true,
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
      //   isActive: true,
    };
    const response = await axios.post(`${baseURL}/taxons/list`, query);
    if (response.status === 200) {
      catogories.value = response.data.rows;
    } else {
      console.log("jiijii");
    }
  } catch (err) {
    console.log(err);
  }
};

const addProduct = async () => {
  try {
    const response = await axios.post(
      `${baseURL}/products/create`,
      productToAdded.value
    );
    if (response.status === 201) {
      toast.success("Амжилттай нэмэгдлээ");
      router.push("/products");
    } else {
      toast.success("Алдаа гарлаа.");
    }
  } catch (err) {
    console.log(err);
  }
};

onMounted(async () => {
  await fetchBrands();
  await fetchCatogeries();
});
</script>
