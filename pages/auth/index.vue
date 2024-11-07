<template>
  <div class="d-flex w-100 justify-center align-center" style="height: 100vh">
    <v-card
      class="pa-8 d-flex flex-column justify-space-between"
      style="width: 30%; min-width: 450px"
      rounded="xl"
      elevation="4"
    >
      <div>
        <div class="d-flex justify-center">
          <img
            style="width: 30%"
            src="https://www.shutterstock.com/image-vector/shopping-logo-ecommerce-logotype-shooping-260nw-1978607771.jpg"
            alt=""
          />
        </div>
        <v-text-field
          class="mt-4"
          rounded="xl"
          variant="outlined"
          label="Утасны дугаар"
          v-model="userToLogin.userName"
        ></v-text-field>
        <v-text-field
          rounded="xl"
          variant="outlined"
          label="Нууц үг"
          :append-inner-icon="
            passwordFieldType === 'password' ? 'mdi-eye' : 'mdi-eye-off'
          "
          @click:append-inner="togglePasswordVisibility"
          :type="passwordFieldType"
          v-model="userToLogin.password"
          @keyup.enter="login"
        >
        </v-text-field>
      </div>

      <div class="d-flex flex-column align-center">
        <v-btn
          block
          elevation="0"
          color="primary"
          style="height: 48px"
          rounded="xl"
          v-model="userToLogin"
          @click="login()"
          >Нэвтрэх</v-btn
        >
        <v-btn
          rounded="lg"
          width="130px"
          variant="text"
          style="font-size: 10px; height: 24px"
          class="mt-2"
          @click="router.push('/auth/register')"
          >Бүртгэл үүсгэх</v-btn
        >
      </div>
    </v-card>
  </div>
</template>

<script lang="ts" setup>
definePageMeta({
  middleware: ['login']
});

import axios from "axios";
import { ref, onMounted } from "vue";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";
import { useRouter } from "vue-router";

const config = useRuntimeConfig();
const baseURL = config.public.baseURL;
const router = useRouter();

const passwordFieldType = ref<any>("password");
const userToLogin = ref<any>({});

const login = async () => {
  try {
    const response = await axios.post(
      `${baseURL}/users/login`,
      userToLogin.value
    );
    if (response.status === 200) {
      localStorage.setItem("userId", response.data.user);
      localStorage.setItem("avatar" , response.data.avatar);
      router.push("/dashboard");
    } else {
      console.log("jiijii");
    }
  } catch (err) {
    console.log(err);
  }
};

const togglePasswordVisibility = () => {
  passwordFieldType.value =
    passwordFieldType.value === "password" ? "text" : "password";
};
</script>
