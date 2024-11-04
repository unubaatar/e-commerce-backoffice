<template>
  <div>
    <div style="font-size: 28px; font-weight: 400">Брендийн жагсаалт</div>
    <div class="d-flex justify-space-between mt-2 w-100">
      <v-text-field
        rounded="xl"
        prepend-inner-icon="mdi-magnify"
        density="compact"
        hide-details
        max-width="300"
        label="Бренд хайх"
        variant="outlined"
      ></v-text-field>
      <div>
        <div class="w-100 text-end mb-2 pr-4" style="font-weight: 500">
          Нийт: <span class="ml-1">{{ brandsCount }}</span>
        </div>
        <v-btn
          @click="showBrandDialog = true"
          rounded="xl"
          prepend-icon="mdi-plus"
          color="primary"
        >
          Бренд нэмэх</v-btn
        >
      </div>
    </div>

    <v-row class="mt-4">
      <v-col v-for="brand in brands" cols="12" md="3" lg="2">
        <v-card
          @click="
            selectedBrand = brand;
            showUpdateBrandDialog = true;
          "
          style="cursor: pointer"
          rounded="lg"
          class="pa-4"
        >
          <div class="d-flex justify-center w-100">
            <img
              style="width: 120px; height: 120px"
              :src="brand.image"
              alt=""
            />
          </div>
          <div
            class="mt-4 w-100 text-center"
            style="font-size: 20px; font-weight: 500"
          >
            {{ brand.name }}
          </div>
        </v-card>
      </v-col>
    </v-row>


    

    <v-dialog width="400" v-model="showUpdateBrandDialog">
      <v-card class="pa-4">
        <div
          class="d-flex justify-center w-100"
          style="font-size: 20px; font-weight: bold"
        >
          Бренд шинэчлэх
        </div>

        <v-row class="mx-2 my-4">
          <v-text-field
            label="Нэр"
            hide-details
            variant="outlined"
            density="compact"
            v-model="selectedBrand.name"
          ></v-text-field>
        </v-row>
        <v-row class="mx-2 my-4 mb-0">
          <v-text-field
            label="Зураг"
            hide-details
            variant="outlined"
            density="compact"
            v-model="selectedBrand.image"
          ></v-text-field>
        </v-row>

        <v-row class="mx-2 my-4 mt-0">
          <div class="d-flex align-center">
            <div>Идэвхтэй болгох:</div>
            <v-switch color="primary" class="ml-4" hide-details v-model="selectedBrand.isActive"></v-switch>
          </div>
        </v-row>
        <div class="d-flex w-100 justify-center">
          <img
            style="height: 200px; width: 200px"
            :src="
              selectedBrand.image == ''
                ? 'https://www.shutterstock.com/image-vector/default-ui-image-placeholder-wireframes-600nw-1037719192.jpg'
                : selectedBrand.image
            "
            alt=""
          />
        </div>

        <div class="d-flex justify-end w-100 mt-4">
          <v-btn
            variant="outlined"
            class="mr-4"
            color="red"
            @click="showUpdateBrandDialog = false"
          >
            Хаах
          </v-btn>
          <v-btn
            @click="updateBrand()"
            variant="outlined"
            rounded="lg"
            color="primary"
            >Засах</v-btn
          >
        </div>
      </v-card>
    </v-dialog>

    <v-dialog width="400" v-model="showBrandDialog">
      <v-card class="pa-4">
        <div
          class="d-flex justify-center w-100"
          style="font-size: 20px; font-weight: bold"
        >
          Бренд нэмэх
        </div>

        <v-row class="mx-2 my-4">
          <v-text-field
            label="Нэр"
            hide-details
            variant="outlined"
            density="compact"
            v-model="brandToAdd.name"
          ></v-text-field>
        </v-row>
        <v-row class="mx-2 my-4">
          <v-text-field
            label="Зураг"
            hide-details
            variant="outlined"
            density="compact"
            v-model="brandToAdd.image"
          ></v-text-field>
        </v-row>
        <div class="d-flex w-100 justify-center">
          <img
            style="height: 200px; width: 200px"
            :src="
              brandToAdd.image == ''
                ? 'https://www.shutterstock.com/image-vector/default-ui-image-placeholder-wireframes-600nw-1037719192.jpg'
                : brandToAdd.image
            "
            alt=""
          />
        </div>

        <div class="d-flex justify-end w-100 mt-4">
          <v-btn
            variant="outlined"
            class="mr-4"
            color="red"
            @click="showBrandDialog = false"
          >
            Хаах
          </v-btn>
          <v-btn
            @click="addBrand()"
            variant="outlined"
            rounded="lg"
            color="primary"
            >Нэмэх</v-btn
          >
        </div>
      </v-card>
    </v-dialog>
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
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";

const brands = ref<any>([]);
const brandsCount = ref<any>([]);
const showBrandDialog = ref<any>(false);
const showUpdateBrandDialog = ref<any>(false);
const selectedBrand = ref<any>({});
const brandToAdd = ref<any>({
  name: "",
  image: "",
});

const addBrand = async () => {
  try {
    const response = await axios.post(
      `${baseURL}/brands/create`,
      brandToAdd.value
    );
    if (response.status === 201) {
      toast.success("Амжилттай");
      await fetchBrands();
      brandToAdd.value.name = "";
      brandToAdd.value.image = "";
      showBrandDialog.value = false;
    } else {
      toast.error("Jiijii");
    }
  } catch (err) {
    console.log(err);
  }
};

const updateBrand = async () => {
  try {
    const response = await axios.post(
      `${baseURL}/brands/update`,
      selectedBrand.value
    );
    if (response.status === 200) {
      toast.success("Амжилттай");
      showUpdateBrandDialog.value = false;
      selectedBrand.value.name = "";
      selectedBrand.value.image = "";
      await fetchBrands();
    } else {
      toast.error("Jiijii");
    }
  } catch (err) {
    console.log(err);
  }
};

const fetchBrands = async () => {
  try {
    const query = {
      page: 1,
      per_page: 10,
    };
    const response = await axios.post(`${baseURL}/brands/list`, query);
    if (response.status === 200) {
      brands.value = response.data.rows;
      brandsCount.value = response.data.count;
    } else {
      console.log("jiijii");
    }
  } catch (err) {
    console.log(err);
  }
};

onMounted(async () => {
  await fetchBrands();
});
</script>
