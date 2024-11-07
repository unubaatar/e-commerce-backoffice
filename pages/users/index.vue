<template>
  <div>
    <div style="font-size: 28px; font-weight: 400">Бүтээгдхүүний жагсаалт</div>

    <div class="my-4">
      <v-data-table hide-default-footer :headers="headers" :items="users">
        <template v-slot:item.image="{ item }: any">
          <div style="cursor: pointer">
            <img
              class="my-2"
              style="height: 40px; width: 40px; border-radius: 50%"
              :src="item.avatar"
              alt=""
            />
          </div>
        </template>

        <template v-slot:item.name="{ item }: any">
          <div>
            <div style="font-weight: bold" >{{ item.firstName }}</div>
            <div >{{ item.lastName }}</div>
          </div>
        </template>

        <template v-slot:item.createdAt="{ item }: any">
          <div>
            {{ moment(item.ceratedAt).format("YYYY/MM/DD-HH:mm:SS") }}
          </div>
        </template>

        <template v-slot:item.action="{ item }: any">
          <div>
            <v-btn size="small" variant="text" icon="mdi-pencil"> </v-btn>
          </div>
        </template>
      </v-data-table>
    </div>

    <v-pagination class="mt-4"></v-pagination>

  </div>
</template>

<script setup lang="ts">
definePageMeta({
  layout: "layout",
  middleware: ["auth"],
});

import { ref } from "vue";
import { useRouter } from "vue-router";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";
import axios from "axios";
import moment from "moment";

const config = useRuntimeConfig();
const baseURL = config.public.baseURL;
const router = useRouter();

const users = ref<any>([]);
const userCount = ref<any>(0);

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
    title: "Утас",
    value: "phone",
    align: "center",
    sortable: false,
  },
  {
    title: "Email",
    value: "email",
    align: "center",
    sortable: false,
  },
  {
    title: "Role",
    value: "role",
    align: "center",
    sortable: false,
  },
  {
    title: "Элссэн огноо",
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

const getUsers = async () => {
  try {
    const response = await axios.post(`${baseURL}/users/list`);
    if (response.status === 200) {
      users.value = response.data.rows;
      userCount.value = response.data.count;
    } else {
      console.log("jiijii");
    }
  } catch (err) {
    console.log(err);
  }
};

onMounted(async () => {
  await getUsers();
});
</script>
