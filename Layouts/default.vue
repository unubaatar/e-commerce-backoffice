<template>
  <v-layout>
    <div style="position: fixed" class="d-flex w-100">
      <v-card
        rounded="lg"
        class="ma-4 pa-4"
        elevation="0"
        style="height: 100vh; min-width: 300px"
      >
        <v-list v-for="item in sideBarItems">
          <div class="px-6" style="font-weight: 600;">{{ item.name }}</div>

          <div v-for="subItem in item.items">
            <v-hover>
              <template v-slot:default="{ isHovering, props }">
                <div
                  :style="isHovering ? 'background-color: #d9d9d9' : ''"
                  v-bind="props"
                  style="font-size: 20px; cursor: pointer; border-radius: 16px"
                  class="d-flex pa-3 ma-3"
                  @click="goToPage(subItem.link)"
                >
                  <v-icon class="mr-3">{{ subItem.icon }}</v-icon>
                  <div style="font-size: 20px">{{ subItem.name }}</div>
                </div>
              </template>
            </v-hover>
          </div>
        </v-list>
      </v-card>

      <div style="width: 100%">
        <v-card
          class="my-4"
          rounded="lg"
          style="width: 98%; height: 80px; background-color: white"
          elevation="0"
        ></v-card>
      </div>
    </div>
    <v-main style="margin-left: 340px; margin-top: 120px">
      <slot />
    </v-main>
  </v-layout>
</template>

<script lang="ts" setup>
import { ref } from "vue";
import { useRouter } from "vue-router";
definePageMeta({});

const router = useRouter();

const sideBarItems = ref<any>([
  {
    name: "Нүүр хуудас",
    items: [
      {
        name: "Dashboard",
        icon: "mdi-view-dashboard",
        link: "/dashboard",
      },
    ],
  },
  {
    name: "Захиалга",
    items: [
      {
        name: "Захиалга",
        icon: "mdi-cart",
        link: "/orders",
      },
    ],
  },
  {
    name: "Нүүр хуудас",
    items: [
      {
        name: "Бүтээгдхүүн",
        icon: "mdi-package-variant",
        link: "/products",
      },
      {
        name: "Бренд",
        icon: "mdi-bootstrap",
        link: "/brands",
      },
      {
        name: "Төрөл",
        icon: "mdi-shape-outline",
        link: "/category",
      },
    ],
  },
  {
    name: "Хэрэглэгчид",
    items: [
      {
        name: "Хэрэглэгчид",
        icon: "mdi-account",
        link: "/users",
      },
    ],
  },
  {
    name: "Систем",
    items: [
      {
        name: "Тохиргоо",
        icon: "mdi-cog",
        link: "/systemOptions",
      },
    ],
  },
]);

const goToPage = (link : any) => {
  router.push(link);
}
</script>

<style scoped></style>
