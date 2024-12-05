<template>
  <div>
    <div style="font-size: 28px; font-weight: 400">Захиалгын жагсаалт</div>
    <div class="w-100 text-end pr-4">
      Нийт захиалгын тоо: <span class="ml-3">{{ count }}</span>
    </div>

    <v-row>
      <v-col cols="12" md="3"
        ><v-text-field
          @keyup.enter="fetchOrders()"
          v-model="filter.orderNumber"
          variant="outlined"
          label="Захиалгын дугаар"
        ></v-text-field
      ></v-col>
      <v-col cols="12" md="3"
        ><v-text-field
          @keyup.enter="fetchOrders()"
          v-model="filter.phone"
          variant="outlined"
          label="Утасны дугаар"
        ></v-text-field
      ></v-col>
      <v-col cols="12" md="3"
        ><v-select
          v-model="filter.orderStatus"
          :items="states"
          variant="outlined"
          item-value="value"
          item-title="name"
          label="Төлөв"
          @update:modelValue="fetchOrders"
        ></v-select
      ></v-col>
    </v-row>

    <v-data-table
      class="mt-4"
      dense
      :items="orders"
      :headers="headers"
      hide-default-footer
    >
      <template v-slot:item.seq="{ index }: any">
        <div>{{ (page - 1) * per_page + index + 1 }}</div>
      </template>

      <template v-slot:item.orderNumber="{ item }: any">
        <div>
          <v-chip style="cursor: pointer" size="small">
            {{ item.orderNumber }}</v-chip
          >
        </div>
      </template>

      <template v-slot:item.customer="{ item }: any">
        <div>
          <span>{{ item.customer.lastname }}</span>
          <span class="ml-2">{{ item.customer.firstname }}</span>
        </div>
      </template>

      <template v-slot:item.detail="{ item }: any">
        <div>
          <div class="mb-6 mt-2 d-flex" v-for="orderItem in item.items">
            <img
              style="height: 50px; width: 50px"
              :src="
                orderItem.variant
                  ? orderItem?.variant?.images[0]
                  : orderItem?.product?.thumbnails[0]
              "
              alt=""
            />
            <div class="ml-4">
              <p class="text-start" style="font-size: 13px">
                {{ orderItem?.product?.name }}
              </p>
              <p
                class="text-start"
                style="font-size: 13px"
                v-if="orderItem?.variant"
              >
                {{ orderItem?.variant?.name }}
              </p>
              <p style="font-size: 13px" class="mt-2 text-start">
                Тоо ширхэг: {{ orderItem?.qty }}
              </p>
              <p style="font-size: 13px" class="text-start">
                Үнэ:
                {{
                  orderItem?.salePrice
                    ? orderItem?.salePrice?.toLocaleString()
                    : orderItem?.price?.toLocaleString()
                }}
              </p>
            </div>
          </div>
        </div>
      </template>

      <template v-slot:item.phone="{ item }: any">
        <div>{{ item.customer.phone }}</div>
      </template>

      <template v-slot:item.totalAmount="{ item }: any">
        <div>{{ item?.totalAmount?.toLocaleString() }}₮</div>
      </template>

      <template v-slot:item.createdAt="{ item }: any">
        <div>{{ moment(item.createdAt).format("YYYY-MM-DD HH:mm:SS") }}</div>
      </template>

      <template v-slot:item.status="{ item }: any">
        <div>
          <v-chip
            variant="flat"
            size="small"
            :color="formatStatesColor(item.orderStatus)"
            >{{ formatStates(item.orderStatus) }}</v-chip
          >
        </div>
      </template>

      <template v-slot:item.action="{ item }: any">
        <div>
          <v-btn
            @click="
              selectedItem = item;
              showDetailDialog = true;
            "
            icon="mdi-pencil"
            elevation="0"
            size="small"
          ></v-btn>
        </div>
      </template>
    </v-data-table>

    <v-pagination
      class="mt-4"
      :length="length"
      v-model="page"
      @update:modelValue="fetchOrders()"
    >
    </v-pagination>

    <v-dialog v-model="showDetailDialog" width="500">
      <v-card rounded="lg" class="pa-4 pb-6">
        <div
          class="w-100 text-center"
          style="font-weight: bolder; font-size: 22px"
        >
          Захиалгын дэлгэрэнгүй
        </div>
        <div class="text-center">{{ selectedItem?.orderNumber }}</div>
        <v-row class="mt-2">
          <v-col cols="6">Хот/Аймаг:</v-col>
          <v-col cols="6">{{ selectedItem?.address?.state }}</v-col>
          <v-col cols="6">Дүүрэг/Сум:</v-col>
          <v-col cols="6">{{ selectedItem?.address?.district }}</v-col>
          <v-col cols="6">Хаяг:</v-col>
          <v-col cols="6">{{ selectedItem?.address?.street }}</v-col>
          <v-col cols="6">Тэмдэглэл:</v-col>
          <v-col cols="6">{{ selectedItem?.address?.note }}</v-col>
          <v-col cols="6">Төлөв:</v-col>
          <v-col cols="6"
            ><v-select
              :items="states"
              item-value="value"
              item-title="name"
              v-model="selectedItem.orderStatus"
              density="compact"
              variant="outlined"
            ></v-select
          ></v-col>
        </v-row>

        <div class="mt-2 d-flex justify-end">
          <v-btn
            variant="outlined"
            color="primary"
            @click="
              updateOrder();
              showDetailDialog = false;
            "
            >Шинэчлэх</v-btn
          >
        </div>
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
import { useRouter } from "vue-router";
import moment from "moment";
import { useRuntimeConfig } from "nuxt/app";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";

const config = useRuntimeConfig();
const baseURL = config.public.baseURL;
const router = useRouter();

const orders = ref<any>([]);
const count = ref<any>(0);
const length = ref<any>(0);
const page = ref<any>(1);
const per_page = ref<any>(10);
const selectedItem = ref<any>({});
const showDetailDialog = ref<any>(false);
const filter = ref<any>({});

const states = ref<any>([
  {
    value: "pending",
    name: "Хүлээгдэж буй",
  },
  {
    value: "shipped",
    name: "Хүргэлтэнд",
  },
  {
    value: "complete",
    name: "Дууссан",
  },
  {
    value: "cancelled",
    name: "Цуцалсан",
  },
]);

const headers = ref<any>([
  {
    title: "#",
    value: "seq",
    align: "center",
    sortable: false,
  },
  {
    title: "Захиалгын дугаар",
    value: "orderNumber",
    align: "center",
    sortable: false,
  },
  {
    title: "Хэрэглэгч",
    value: "customer",
    align: "center",
    sortable: false,
  },
  {
    title: "Утасны дугаар",
    value: "phone",
    align: "center",
    sortable: false,
  },
  {
    title: "Дэлгэрэнгүй",
    value: "detail",
    align: "center",
    sortable: false,
  },
  {
    title: "Нийт үнийн дүн",
    value: "totalAmount",
    align: "center",
    sortable: false,
  },
  {
    title: "Төлөв",
    value: "status",
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
    title: "Үйлдэл",
    value: "action",
    align: "center",
    sortable: false,
  },
]);

const fetchOrders = async () => {
  try {
    const query = {
      page: page.value,
      per_page: per_page.value,
      filter: filter.value,
    };
    const response = await axios.post(`${baseURL}/orders/list`, query);
    if (response.status === 200) {
      orders.value = response.data.rows;
      count.value = response.data.count;
    } else {
      console.log("jiijii");
    }
  } catch (err) {
    console.log(err);
  }
};

const updateOrder = async () => {
  try {
    const response = await axios.post(
      `${baseURL}/orders/update`,
      selectedItem.value
    );
    if (response.status === 200) {
      toast.success("Амжилттай");
      await fetchOrders();
    } else {
      console.log("jiijii");
    }
  } catch (err) {
    console.log(err);
  }
};

const formatStates = (item: any) => {
  switch (item) {
    case "pending":
      return "Хүлээгдэж буй";
    case "shipped":
      return "Хүрэгэлтэнд";
    case "complete":
      return "Дууссан";
    case "cancelled":
      return "Цуцалсан";
  }
};

const formatStatesColor = (item: any) => {
  switch (item) {
    case "pending":
      return "yellow";
    case "shipped":
      return "blue";
    case "complete":
      return "green";
    case "cancelled":
      return "red";
  }
};

onMounted(async () => {
  await fetchOrders();
  length.value = Math.ceil(count.value / per_page.value);
});
</script>
