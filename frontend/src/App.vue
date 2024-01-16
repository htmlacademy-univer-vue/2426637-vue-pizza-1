<template>
  <app-layout>
    <router-view v-slot="{ Component }">
      <transition name="slide" mode="out-in">
        <component :is="Component" />
      </transition>
    </router-view>
  </app-layout>
</template>

<script setup>
import AppLayout from "@/layouts/AppLayout.vue";
import { onMounted, ref } from "vue";
import { useDataStore } from "@/stores/data";
import { useAuthStore } from "@/stores/auth";
import JwtService from "@/services/jwt/jwt.service";
import router from "@/router";
import { useRoute } from "vue-router";

const dataStore = useDataStore();
const route = useRoute();
const isLoaded = ref(false);

const checkLoggedIn = async () => {
  const authStore = useAuthStore();
  const token = JwtService.getToken();
  if (!token) {
    isLoaded.value = true;
    return;
  }

  try {
    await authStore.whoami();
    const { redirect } = route.query;
    await router.push(redirect ? redirect : { name: "home" });
  } catch (e) {
    JwtService.destroyToken();
    console.error(e);
  } finally {
    isLoaded.value = true;
  }
};

onMounted(() => {
  checkLoggedIn();
  dataStore.loadData();
});
</script>

<style lang="scss">
@import "@/assets/scss/app.scss";
.slide-enter-active,
.slide-leave-active {
  transition: all .25s ease-in-out;
}

.slide-enter-from {
  opacity: 0;
  transform: translateX(25%);
}

.slide-leave-to {
  opacity: 0;
  transform: translateX(-25%);
}

.slide-enter-to,
.slide-leave-from {
  opacity: 1;
  transform: translateX(0);
}
</style>