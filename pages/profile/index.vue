<template>
  <div>
    {{ user }}
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

const user = ref<string | null>(null);

onMounted(() => {
  const storedUser = localStorage.getItem("user");
  user.value = storedUser ? JSON.parse(storedUser) : null;
});
</script>
