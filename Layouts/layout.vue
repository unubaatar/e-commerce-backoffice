<template>
  <v-layout>
    <v-card
      rounded="lg"
      class="ma-4 pa-4"
      elevation="0"
      style="
        height: 100%;
        min-width: 300px;
        width: 300px;
        position: fixed;
        z-index: 10;
      "
    >
      <v-list v-for="item in sideBarItems">
        <div class="px-6" style="font-weight: 600; font-size: 14px">
          {{ item.name }}
        </div>

        <div v-for="subItem in item.items">
          <v-hover>
            <template v-slot:default="{ isHovering, props }">
              <div
                :style="isHovering ? 'background-color: #d9d9d9' : ''"
                v-bind="props"
                style="font-size: 16px; cursor: pointer; border-radius: 16px"
                class="d-flex pa-3 ma-2"
                @click="goToPage(subItem.link)"
              >
                <v-icon class="mr-2">{{ subItem.icon }}</v-icon>
                <div style="font-size: 16px">{{ subItem.name }}</div>
              </div>
            </template>
          </v-hover>
        </div>
      </v-list>
      <div
        class="d-flex w-100 justify-center"
        style="background-color: white; position: sticky; bottom: 0"
      >
        <img
          style="height: 40px"
          src="https://www.shutterstock.com/image-vector/shopping-logo-ecommerce-logotype-shooping-260nw-1978607771.jpg"
          alt=""
        />
      </div>
    </v-card>

    <div
      style="position: fixed; z-index: 10"
      class="w-100 d-flex justify-center"
    >
      <v-card
        class="my-4"
        rounded="lg"
        style="width: 100%; height: 80px; background-color: white; left: 333px"
        elevation="0"
      ></v-card>
    </div>

    <div
      style="
        position: fixed;
        z-index: 9;
        height: 120px;
        background-color: #e8e8e8;
      "
      class="w-100"
    ></div>

    <v-main class="main" style="margin-top: 120px; margin-left: 333px">
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

const goToPage = (link: any) => {
  router.push(link);
};
</script>

<style scoped>
::-webkit-scrollbar {
  width: 12px;
  height: 12px;
  display: none;
}

::-webkit-scrollbar-track {
  background: #f1f1f1;
  border-radius: 10px;
}

::-webkit-scrollbar-thumb {
  background: #888;
  border-radius: 10px;
}

::-webkit-scrollbar-thumb:hover {
  background: #555;
}

::-webkit-scrollbar-corner {
  background: #f1f1f1;
}
</style>
