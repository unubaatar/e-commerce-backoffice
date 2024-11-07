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
          label="Овог"
          v-model="userToRegister.lastName"
        ></v-text-field>
        <v-text-field
          rounded="xl"
          variant="outlined"
          label="Нэр"
          v-model="userToRegister.firstName"
        ></v-text-field>
        <v-text-field
          rounded="xl"
          variant="outlined"
          label="Утасны дугаар"
          v-model="userToRegister.phone"
        ></v-text-field>
        <v-text-field
          rounded="xl"
          variant="outlined"
          label="Email"
          v-model="userToRegister.email"
        ></v-text-field>
        <v-text-field
          v-model="userToRegister.password"
          rounded="xl"
          variant="outlined"
          label="Нууц үг"
          :append-inner-icon="
            passwordFieldType === 'password' ? 'mdi-eye' : 'mdi-eye-off'
          "
          @click:append-inner="togglePasswordVisibility"
          :type="passwordFieldType"
        >
        </v-text-field>
        <v-text-field
          v-model="userToRegister.repassword"
          rounded="xl"
          variant="outlined"
          label="Нууц үг"
          :append-inner-icon="
            passwordFieldType === 'password' ? 'mdi-eye' : 'mdi-eye-off'
          "
          @click:append-inner="togglePasswordVisibility"
          :type="passwordFieldType"
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
          @click="register()"
          :disabled="disableButton()"
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

import { ref } from "vue";
import { useRouter } from "vue-router";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";
import axios from "axios";

const config = useRuntimeConfig();
const baseURL = config.public.baseURL;
const router = useRouter();

const userToRegister = ref<any>({});
const passwordFieldType = ref<any>("password");

const disableButton = () => {
  if (
    !userToRegister.value.firstName ||
    !userToRegister.value.lastName ||
    !userToRegister.value.password ||
    !userToRegister.value.repassword ||
    !userToRegister.value.email ||
    !userToRegister.value.phone
  ) {
    return true;
  }
  return false;
};

const register = async () => {
  try {
    if (userToRegister.value.password !== userToRegister.value.repassword) {
      toast.error("Нууц үг таарахгүй байна.");
    }
    const response = await axios.post(
      `${baseURL}/users/create`,
      userToRegister.value
    );
    if (response.status === 201) {
      toast.success("Амжилттай");
      await login();
    } else {
      toast.error(response.data.message);
    }
  } catch (err) {
    console.log(err);
  }
};

const login = async () => {
  try {
    const response = await axios.post(
      `${baseURL}/users/login`,
      userToRegister.value
    );
    if (response.status === 200) {
      localStorage.setItem("userId", response.data.user);
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
