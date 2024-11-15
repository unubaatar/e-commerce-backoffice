<template>
  <div>
    <div style="font-size: 28px; font-weight: 400">Бүтээгдхүүний жагсаалт</div>
    <div class="w-100 d-flex justify-end">
      <div>
        <div class="mb-3 w-100 text-end pr-4" style="font-weight: 500">
          Нийт: <span>{{ productCount }}</span>
        </div>
        <v-btn
          @click="goCreateProductPage"
          elevation="0"
          color="primary"
          rounded="xl"
          append-icon="mdi-plus"
          >Бүтээгдхүүн нэмэх</v-btn
        >
      </div>
    </div>
    <div class="my-4 mb-8">
      <v-data-table
        dense
        :items="products"
        :headers="headers"
        hide-default-footer
      >
        <template v-slot:item.image="{ item }: any">
          <div
            style="cursor: pointer"
            @click="router.push(`/products/${item._id}`)"
          >
            <img style="height: 50px" :src="item.thumbnails[0]" alt="" />
          </div>
        </template>

        <template v-slot:item.product="{ item }: any">
          <div>
            {{ item.name }}
          </div>
        </template>

        <template v-slot:item.isActive="{ item }: any">
          <div>
            <v-chip v-if="item.isActive" color="green" size="small"
              >Идэвхтэй</v-chip
            >
            <v-chip v-else color="red" size="small">Идэвхгүй</v-chip>
          </div>
        </template>

        <template v-slot:item.createdAt="{ item }: any">
          <div>
            {{ moment(item.createdAt).format("YYYY/MM/DD - HH:mm:SS") }}
          </div>
        </template>

        <template v-slot:item.category="{ item }: any">
          <div>
            {{ item.category.name }}
          </div>
        </template>

        <template v-slot:item.brand="{ item }: any">
          <div>
            {{ item.brand.name }}
          </div>
        </template>

        <template v-slot:item.price="{ item }: any">
          <div>
            {{ item.price.toLocaleString() }}
          </div>
        </template>

        <template v-slot:item.action="{ item }: any">
          <div>
            <v-btn size="small" variant="text" icon="mdi-pencil"></v-btn>
          </div>
        </template>
      </v-data-table>
      <v-pagination
        class="mt-4"
        :length="length"
        v-model="page"
        @update:modelValue="fetchProducts()"
      >
      </v-pagination>
    </div>
  </div>
</template>

<script setup lang="ts">
definePageMeta({
  layout: "layout",
  middleware: ['auth']
});

import axios from "axios";
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";
import moment from "moment";
import { useRuntimeConfig } from "nuxt/app";

const config = useRuntimeConfig();
const baseURL = config.public.baseURL;
const router = useRouter();
const products = ref<any>([]);
const productCount = ref<any>(0);
const length = ref<any>(0);
const page = ref<any>(1);
const per_page = ref<any>(10);

const headers = ref<any>([
  {
    title: "Зураг",
    value: "image",
    align: "center",
    sortable: false,
  },
  {
    title: "Бүтээгдхүүн",
    value: "product",
    align: "center",
    sortable: false,
  },
  {
    title: "Төлөв",
    value: "isActive",
    align: "center",
    sortable: false,
  },
  {
    title: "Бренд",
    value: "brand",
    align: "center",
    sortable: false,
  },
  {
    title: "Ангилал",
    value: "category",
    align: "center",
    sortable: false,
  },
  {
    title: "Үүссэн огноо",
    value: "createdAt",
    align: "center",
    sortable: false,
  },
  {
    title: "Үнэ",
    value: "price",
    align: "center",
    sortable: false,
  },
  {
    title: "Үйлдэл",
    value: "action",
    align: "center",
    sortable: false,
  },
]);

const goCreateProductPage = () => {
  router.push("/products/create");
};

const fetchProducts = async () => {
  try {
    const query = {
      page: page.value,
      per_page: per_page.value,
    };
    const response = await axios.post(`${baseURL}/products/list`, query);
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
  length.value = Math.ceil(productCount.value / (per_page.value - 1));
});
</script>
