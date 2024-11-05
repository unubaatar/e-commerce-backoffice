<template>
  <div>
    <div style="font-size: 28px; font-weight: 400">Ангилалын жагсаалт</div>
    <div class="d-flex justify-space-between mt-2 w-100">
      <v-text-field
        rounded="xl"
        prepend-inner-icon="mdi-magnify"
        density="compact"
        hide-details
        max-width="300"
        label="Ангилал хайх"
        variant="outlined"
      ></v-text-field>
      <div>
        <div class="w-100 text-end mb-2 pr-4" style="font-weight: 500">
          Нийт: <span class="ml-1">{{ categoryCount }}</span>
        </div>
        <v-btn
          @click="showCategoryDialog = true"
          rounded="xl"
          prepend-icon="mdi-plus"
          color="primary"
        >
          Ангилал нэмэх</v-btn
        >
      </div>
    </div>

    <v-data-table
      hide-default-footer
      dense
      :headers="headers"
      :items="categories"
      class="my-6"
    >
      <template v-slot:item.name="{ item }: any">
        <div style="font-size: 16px">{{ item.name }}</div>
      </template>
      <template v-slot:item.image="{ item }: any">
        <div class="d-flex align-center pa-4">
          <img
            class="mr-4"
            style="height: 50px; width: 50px"
            :src="item.image"
            alt=""
          />
          <div>{{ item.image }}</div>
        </div>
      </template>
      <template v-slot:item.createdAt="{ item }: any">
        <div style="font-size: 16px">
          {{ moment(item.createdAt).format("YYYY/MM/DD HH:mm:SS") }}
        </div>
      </template>
      <template v-slot:item.isActive="{ item }: any">
        <div style="font-size: 16px">
          <v-icon v-if="item.isActive" color="green"
            >mdi-check-decagram-outline</v-icon
          >
          <v-icon v-else color="red">mdi-check-decagram-outline</v-icon>
        </div>
      </template>
      <template v-slot:item.action="{ item }: any">
        <div style="font-size: 16px">
          <v-btn
            @click="
              selectedCategory = item;
              showCategoryUpdateDialog = true;
            "
            size="small"
            icon="mdi-pencil"
            rounded="xl"
            variant="text"
          ></v-btn>
        </div>
      </template>
    </v-data-table>

    <v-dialog width="400" v-model="showCategoryDialog">
      <v-card class="pa-4">
        <div
          class="d-flex justify-center w-100"
          style="font-size: 20px; font-weight: bold"
        >
          Ангилал нэмэх
        </div>

        <v-row class="mx-2 my-4">
          <v-text-field
            label="Нэр"
            hide-details
            variant="outlined"
            density="compact"
            v-model="categoryToAdd.name"
          ></v-text-field>
        </v-row>
        <v-row class="mx-2 my-4">
          <v-text-field
            label="Зураг"
            hide-details
            variant="outlined"
            density="compact"
            v-model="categoryToAdd.image"
          ></v-text-field>
        </v-row>
        <div class="d-flex w-100 justify-center">
          <img
            style="height: 200px; width: 200px"
            :src="
              categoryToAdd.image == ''
                ? 'https://www.shutterstock.com/image-vector/default-ui-image-placeholder-wireframes-600nw-1037719192.jpg'
                : categoryToAdd.image
            "
            alt=""
          />
        </div>

        <div class="d-flex justify-end w-100 mt-4">
          <v-btn
            variant="outlined"
            class="mr-4"
            color="red"
            @click="showCategoryDialog = false"
          >
            Хаах
          </v-btn>
          <v-btn
            variant="outlined"
            rounded="lg"
            color="primary"
            @click="addCategory()"
            >Нэмэх</v-btn
          >
        </div>
      </v-card>
    </v-dialog>

    <v-dialog width="400" v-model="showCategoryUpdateDialog">
      <v-card class="pa-4">
        <div
          class="d-flex justify-center w-100"
          style="font-size: 20px; font-weight: bold"
        >
          Ангилал шинэчлэх
        </div>

        <v-row class="mx-2 my-4">
          <v-text-field
            label="Нэр"
            hide-details
            variant="outlined"
            density="compact"
            v-model="selectedCategory.name"
          ></v-text-field>
        </v-row>
        <v-row class="mx-2 my-4">
          <v-text-field
            label="Зураг"
            hide-details
            variant="outlined"
            density="compact"
            v-model="selectedCategory.image"
          ></v-text-field>
        </v-row>
        <div class="d-flex w-100 justify-center">
          <img
            style="height: 200px; width: 200px"
            :src="
              selectedCategory.image == ''
                ? 'https://www.shutterstock.com/image-vector/default-ui-image-placeholder-wireframes-600nw-1037719192.jpg'
                : selectedCategory.image
            "
            alt=""
          />
        </div>

        <v-row class="mx-2 my-4 mt-0">
          <div class="d-flex align-center">
            <div>Идэвхтэй болгох:</div>
            <v-switch color="primary" class="ml-4" hide-details v-model="selectedCategory.isActive"></v-switch>
          </div>
        </v-row>

        <div class="d-flex justify-end w-100 mt-4">
          <v-btn
            variant="outlined"
            class="mr-4"
            color="red"
            @click="showCategoryUpdateDialog = false"
          >
            Хаах
          </v-btn>
          <v-btn
            @click="updateCategory()"
            variant="outlined"
            rounded="lg"
            color="primary"
            >Засах</v-btn
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
import moment from "moment";

const headers = ref<any>([
  {
    title: "Нэр",
    value: "name",
    align: "center",
    sortable: false,
  },
  {
    title: "Зураг",
    align: "center",
    value: "image",
    sortable: false,
  },
  {
    title: "Идэвхтэй эсэх",
    align: "center",
    value: "isActive",
    sortable: false,
  },
  {
    title: "Үүссэн огноо",
    align: "center",
    value: "createdAt",
    sortable: false,
  },
  {
    title: "Засах",
    align: "center",
    value: "action",
    sortable: false,
  },
]);

const categories = ref<any>([]);
const categoryCount = ref<any>(0);
const showCategoryDialog = ref<any>(false);
const showCategoryUpdateDialog = ref<any>(false);
const selectedCategory = ref<any>({
  image: "",
  name: "",
});
const categoryToAdd = ref<any>({
  image: "",
  name: "",
});

const fetchCatogeries = async () => {
  try {
    const query = {
      page: 1,
      per_page: 10,
    };
    const response = await axios.post(`${baseURL}/categories/list`, query);
    if (response.status === 200) {
      categories.value = response.data.rows;
      categoryCount.value = response.data.count;
    } else {
      console.log("jiijii");
    }
  } catch (err) {
    console.log(err);
  }
};

const addCategory = async () => {
  try {
    const response = await axios.post(
      `${baseURL}/categories/create`,
      categoryToAdd.value
    );
    if (response.status === 201) {
      toast.success("Амжилттай");
      await fetchCatogeries();
      categoryToAdd.value.name = "";
      categoryToAdd.value.image = "";
      showCategoryDialog.value = false;
    } else {
      toast.error("Алдаа заалаа.");
    }
  } catch (err) {
    console.log(err);
  }
};

const updateCategory = async () => {
  try {
    const response = await axios.post(
      `${baseURL}/categories/update`,
      selectedCategory.value
    );
    if (response.status === 200) {
      toast.success("Амжилттай");
      await fetchCatogeries();
      selectedCategory.value.name = "";
      selectedCategory.value.image = "";
      showCategoryUpdateDialog.value = false;
    } else {
      toast.error("Алдаа заалаа.");
    }
  } catch (err) {
    console.log(err);
  }
};

onMounted(async () => {
  await fetchCatogeries();
});
</script>

<style scoped></style>
