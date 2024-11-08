<template>
  <div>
    <div style="font-size: 28px; font-weight: 400">Бүтээгдхүүн дэлгэрэнгүй</div>
    <v-card class="pa-8 my-6 mb-12" rounded="lg" elevation="0">
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
        <v-btn @click="updateProduct()" variant="flat" color="primary"
          >Бүтээгдхүүн засах</v-btn
        >
      </div>

      <div class="mt-4">
        <div class="d-flex justify-space-between">
          <div style="font-size: 28px; font-weight: 400">Төрөл нэмэх</div>
          <div>
            <v-btn
              @click="showAddVariantDialog = true"
              variant="outlined"
              color="primary"
              >Төрөл нэмэх</v-btn
            >
          </div>
        </div>
        <v-data-table
          class="mt-4"
          hide-default-footer
          :headers="headers"
          :items="items"
        >
          <template v-slot:item.image="{ item }: any">
            <div class="pa-2">
              <img
                :src="item.images[0]"
                style="width: 48x; height: 48px"
                alt=""
              />
            </div>
          </template>
          <template v-slot:item.name="{ item }: any">
            <div class="pa-2">
              {{ item.name }}
            </div>
          </template>
          <template v-slot:item.price="{ item }: any">
            <div class="pa-2">
              {{ item.price.toLocaleString() }}
            </div>
          </template>
          <template v-slot:item.sellPrice="{ item }: any">
            <div class="pa-2">
              {{ item.sellPrice.toLocaleString() }}
            </div>
          </template>
          <template v-slot:item.status="{ item }: any">
            <div class="pa-2">
              <v-icon v-if="item.isActive" color="green"
                >mdi-check-decagram-outline</v-icon
              >
              <v-icon v-else color="red">mdi-close-circle-outline</v-icon>
            </div>
          </template>
          <template v-slot:item.createdAt="{ item }: any">
            <div class="pa-2">
              {{ moment(item.createdAt).format("YYYY/MM/DD - HH:mm:SS") }}
            </div>
          </template>
          <template v-slot:item.action="{ item }: any">
            <div class="pa-2">
              <v-btn
                @click="
                  selectedVariant = item;
                  showUpdateDialog = true;
                "
                icon="mdi-pencil"
                size="small"
                variant="text"
              ></v-btn>
            </div>
          </template>
        </v-data-table>
      </div>
    </v-card>

    <v-dialog v-model="showAddVariantDialog" max-width="600">
      <v-card class="pa-4" rounded="lg">
        <div
          class="w-100 text-center"
          style="font-weight: bold; font-size: 20px"
        >
          Төрөл нэмэх
        </div>
        <v-row class="mt-4">
          <v-col cols="6">
            <v-text-field
              v-model="variantToAdd.name"
              variant="outlined"
              label="Нэр"
            ></v-text-field>
          </v-col>
          <v-col cols="6"
            ><v-text-field
              v-model="variantToAdd.image"
              variant="outlined"
              label="Зураг"
            ></v-text-field
          ></v-col>
          <v-col cols="6"
            ><v-text-field
              v-model="variantToAdd.price"
              variant="outlined"
              label="Үндсэн үнэ"
            ></v-text-field
          ></v-col>
          <v-col cols="6"
            ><v-text-field
              v-model="variantToAdd.sellPrice"
              variant="outlined"
              label="Хямдарсан үнэ"
            ></v-text-field
          ></v-col>
        </v-row>
        <v-row class="mb-4 mx-2" justify="end">
          <v-btn @click="addVariant()" color="primary">Төрөл нэмэх</v-btn>
        </v-row>
      </v-card>
    </v-dialog>

    <v-dialog v-model="showUpdateDialog" width="500">
      <v-card class="pa-4" rounded="lgx">
        <div
          class="w-100 text-center"
          style="font-weight: bold; font-size: 20px"
        >
          Төрөл шинэчлэх
        </div>

        <v-row class="mt-4">
          <v-col cols="6">
            <v-text-field
              v-model="selectedVariant.name"
              variant="outlined"
              label="Нэр"
            ></v-text-field>
          </v-col>
          <v-col cols="6">
            <div class="d-flex ">
              <div class="mt-3">Идэвхтэй эсэх:</div>
              <v-switch
              class="ml-4 mt-2"
                color="primary"
                v-model="selectedVariant.isActive"
                hide-details
                density="compact"
              ></v-switch>
            </div>
          </v-col>
          <v-col cols="6"
            ><v-text-field
              v-model="selectedVariant.price"
              variant="outlined"
              label="Үндсэн үнэ"
            ></v-text-field
          ></v-col>
          <v-col cols="6"
            ><v-text-field
              v-model="selectedVariant.sellPrice"
              variant="outlined"
              label="Хямдарсан үнэ"
            ></v-text-field
          ></v-col>
        </v-row>

        <v-row class="mb-4 mx-2" justify="end">
          <v-btn @click="updateVariant()" color="primary">Төрөл шинэчлэх</v-btn>
        </v-row>
      </v-card>
    </v-dialog>
  </div>
</template>

<script setup lang="ts">
definePageMeta({
  layout: "layout",
  middleware: ["auth"],
});

import axios from "axios";
import { ref, onMounted } from "vue";
import { useRoute, useRouter } from "vue-router";
import { toast } from "vue3-toastify";
import moment from "moment";
import "vue3-toastify/dist/index.css";

const config = useRuntimeConfig();
const baseURL = config.public.baseURL;
const router = useRouter();
const route = useRoute();

const product = ref<any>({});
const brands = ref<any>([]);
const catogories = ref<any>([]);
const showAddVariantDialog = ref<any>(false);
const showUpdateDialog = ref<any>(false);
const variantToAdd = ref<any>({});
const items = ref<any>([]);
const selectedVariant = ref<any>({});

const headers = ref<any>([
  {
    title: "Зураг",
    value: "image",
    align: "center",
    sortable: false,
  },
  {
    title: "Нэр",
    value: "name",
    align: "center",
    sortable: false,
  },
  {
    title: "Үндсэн үнэ",
    value: "price",
    align: "center",
    sortable: false,
  },
  {
    title: "Хямдарсан үнэ",
    value: "sellPrice",
    align: "center",
    sortable: false,
  },
  {
    title: "Статус",
    value: "status",
    align: "center",
    sortable: false,
  },
  {
    title: "Огноо",
    value: "createdAt",
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

const updateProduct = async () => {
  try {
    const response = await axios.post(
      `${baseURL}/products/update`,
      product.value
    );
    if (response.status === 200) {
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

const addVariant = async () => {
  try {
    const query = {
      productId: product.value._id,
      name: variantToAdd.value.name,
      images: variantToAdd.value.image,
      sellPrice: variantToAdd.value.sellPrice,
      price: variantToAdd.value.price,
    };
    const response = await axios.post(
      `${baseURL}/productVariants/create`,
      query
    );
    if (response.status === 201) {
      toast.success("Амжилттай үүслээ");
      showAddVariantDialog.value = false;
      window.location.reload();
    } else {
      toast.error("Алдаа заалаа");
    }
  } catch (err) {
    console.log(err);
  }
};

const updateVariant = async () => {
  try {
    const response = await axios.post(
      `${baseURL}/productVariants/update`,
      selectedVariant.value
    );
    if (response.status === 200) {
      toast.success("Амжилттай шинэчлэгдлээ");
      showUpdateDialog.value = false;
      await getProduct();
    } else {
      toast.error("Алдаа заалаа");
    }
  } catch (err) {
    console.log(err);
  }
};

const goAddVariant = () => {
  router.push(`/addVariant/${product.value._id}`);
};

onMounted(async () => {
  await getProduct();
  await fetchBrands();
  await fetchCatogeries();
  items.value = product.value.variants;
});
</script>
