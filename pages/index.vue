<template>
  <div></div>
</template>

<script lang="ts" setup>
definePageMeta({
  layout: "layout",
  middleware: ["auth"],
});

import { onMounted } from "vue";
import { useRouter } from "vue-router";

const router = useRouter();

onMounted(() => {});
</script>
