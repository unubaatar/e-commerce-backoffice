<template>
  <v-layout>
    <v-navigation-drawer
      tile
      flat
      floating
      persistent
      permanent
      width="300"
      style="background-color: transparent"
    >
      <v-card
        rounded="lg"
        class="pa-6 h-100"
        elevation="0"
        style="min-width: 300px; width: 300px; overflow: auto"
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
    </v-navigation-drawer>

    <div
      style="position: fixed; z-index: 10; padding-right: 24px"
      class="w-100 d-flex justify-center"
    >
      <v-card
        class="my-4 d-flex justify-end align-center pa-4"
        rounded="lg"
        style="
          width: 100%;
          height: 80px;
          background-color: white;
          margin-left: 333px;
        "
        elevation="0"
      >
        <v-menu>
          <template v-slot:activator="{ props }">
            <div
              v-bind="props"
              style="cursor: pointer"
              class="d-flex align-center"
            >
              <img
                style="width: 50px; height: 50px; border-radius: 50%"
                :src="user.avatar"
                alt=""
              />
              <div class="mx-4">
                <p style="font-weight: bold">{{ user.firstName }}</p>
                <p style="font-size: 13px">{{ formatRole() }}</p>
              </div>
            </div>
          </template>
          <v-list>
            <v-list-item
              @click="router.push('/profile')"
              style="cursor: pointer"
            >
              <v-icon class="mr-3">mdi-account</v-icon>
              Profile</v-list-item
            >
            <v-list-item @click="logOut()" style="cursor: pointer"
              ><v-icon class="mr-3">mdi-logout</v-icon>Log out</v-list-item
            >
          </v-list>
        </v-menu>
      </v-card>
    </div>

    <div
      style="
        position: fixed;
        z-index: 9;
        height: 100px;
        background-color: #e8e8e8;
      "
      class="w-100"
    ></div>

    <v-main style="margin-top: 120px; padding-left: 350px; padding-right: 24px">
      <slot />
    </v-main>
  </v-layout>
</template>

<script lang="ts" setup>
definePageMeta({});

import { ref } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";

const router = useRouter();

const avatar = ref<any>("");
const config = useRuntimeConfig();
const baseURL = config.public.baseURL;
const user = ref<any>({});

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

const formatRole = () => {
  switch (user.value.role) {
    case "admin":
      return "Админ";
    case "productManager":
      return "Бүтээгдхүүн хөгжүүлэгч";
  }
};

const goToPage = (link: any) => {
  router.push(link);
};

const logOut = () => {
  localStorage.removeItem("userId");
  router.push("/auth");
};

onMounted(async () => {
  await getUserData();
});
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
