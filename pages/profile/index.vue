<template>
  <div>
    <div class="d-flex justify-space-between">
      <div style="font-size: 28px; font-weight: 400">Хэрэглэгчийн профайл</div>
      <div>
        <v-btn
          width="150"
          color="primary"
          variant="tonal"
          rounded="lg"
          class="mr-4"
          @click="changePasswordDialog = true"
          >Нууц үг солих</v-btn
        >
        <v-btn
          width="150"
          variant="flat"
          color="primary"
          rounded="lg"
          @click="updateUser()"
          >Хадгалах</v-btn
        >
      </div>
    </div>

    <v-row class="mt-4">
      <v-col cols="8">
        <v-card style="height: 300px" rounded="lg" elevation="0" class="pa-4">
          <v-row>
            <v-col cols="6"
              ><div class="ml-2 mb-2">Овог</div>
              <v-text-field
                v-model="user.lastName"
                variant="outlined"
                rounded="lg"
              ></v-text-field
            ></v-col>
            <v-col cols="6"
              ><div class="ml-2 mb-2">Нэр</div>
              <v-text-field
                v-model="user.firstName"
                variant="outlined"
                rounded="lg"
              ></v-text-field
            ></v-col>
            <v-col cols="6"
              ><div class="ml-2 mb-2">Утасны дугаар</div>
              <v-text-field
                v-model="user.phone"
                variant="outlined"
                rounded="lg"
              ></v-text-field
            ></v-col>
            <v-col cols="6"
              ><div class="ml-2 mb-2">E-mail</div>
              <v-text-field
                v-model="user.email"
                variant="outlined"
                rounded="lg"
              ></v-text-field
            ></v-col>
          </v-row>
        </v-card>
      </v-col>
      <v-col cols="4">
        <v-card style="height: 300px" rounded="lg" elevation="0" class="pa-4">
          <div class="d-flex justify-center align-center flex-column">
            <img
              :src="user.avatar"
              alt=""
              style="height: 180px; width: 180px; border-radius: 50%"
            />
            <div class="w-100 text-start">Зурагны URL</div>
            <v-text-field
              v-model="user.avatar"
              style="width: 100%"
              class="mt-2"
              density="compact"
              variant="outlined"
              hide-details
              rounded="lg"
            ></v-text-field>
          </div>
        </v-card>
      </v-col>
    </v-row>

    <v-dialog width="500" v-model="changePasswordDialog">
      <v-card rounded="lg" class="pa-8">
        <div
          class="w-100 text-center mb-4"
          style="font-weight: bold; font-size: 20px"
        >
          Нууц үг солих
        </div>
        <v-text-field v-model="changingPassword.password" rounded="lg" label="Одоогийн нууц" variant="outlined">
        </v-text-field>
        <v-text-field v-model="changingPassword.newPassword" rounded="lg" label="Шинэ нууц үг" variant="outlined">
        </v-text-field>
        <v-text-field v-model="changingPassword.rePassword" rounded="lg" label="Дахин давтана уу" variant="outlined">
        </v-text-field>
        <div class="d-flex justify-end">
          <v-btn color="primary" @click="changePassword()">Нууц үг солих</v-btn>
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
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";
import moment from "moment";
import { useRuntimeConfig } from "nuxt/app";

const config = useRuntimeConfig();
const baseURL = config.public.baseURL;
const router = useRouter();

const user = ref<any>({});
const changingPassword = ref<any>({});
const changePasswordDialog = ref<any>(false);

const getUserData = async () => {
  try {
    const query = {
      _id: localStorage.getItem("userId"),
    };
    const response = await axios.post(`${baseURL}/users/getById`, query);
    if (response.status === 200) {
      user.value = response.data;
    } else {
      console.log("jiijii");
    }
  } catch (err) {
    console.log(err);
  }
};

const changePassword = async() => {
  try {
    if(changingPassword.value.rePassword !== changingPassword.value.newPassword) {
      toast.error("Нууц үг таарахгүй байна");
      return;
    };
    const query = {
      newPassword: changingPassword.value.newPassword,
      password: changingPassword.value.password,
      _id: user.value._id
    };
    const response = await axios.post(`${baseURL}/users/changePassword` , query);
    if(response.status === 200) {
      toast.success("Амжилттай солигдлоо");
    } else {
      toast.error("Алдаа заалаа");
    }
  } catch(err) {
    console.log(err);
  }
}

const updateUser = async () => {
  try {
    const response = await axios.post(`${baseURL}/users/update`, user.value);
    if (response.status === 200) {
      toast.success("Амжилттай");
      await getUserData();
    } else {
      toast.error("Алдаа заалаа");
    }
  } catch (err) {}
};

onMounted(async () => {
  const storedUser = localStorage.getItem("user");
  await getUserData();
});
</script>
