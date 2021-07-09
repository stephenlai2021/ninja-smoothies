<template>
  <div class="q-pa-md">
    <q-layout view="lHh Lpr lff">
      <q-header elevated class="bg-cyan-8">
        <q-toolbar>
          <!-- <q-btn v-if="route.fullPath" label="Back" flat dense class="q-mt-xs" to="/" /> -->
          <q-toolbar-title class="absolute-center">Ninja Smoothies</q-toolbar-title>
          <q-btn class="q-ma-sm" position flat @click="drawer = !drawer" round dense icon="menu" />
        </q-toolbar>
      </q-header>

      <q-drawer v-model="drawer" show-if-above :width="200" :breakpoint="400">
        <q-scroll-area
          style="
            height: calc(100% - 150px);
            margin-top: 150px;
            border-right: 1px solid #ddd;
          "
        >
          <q-list padding>
            <q-item clickable v-ripple>
              <q-item-section avatar>
                <q-icon name="home" />
              </q-item-section>
              <q-item-section @click="router.push('/')"> Home </q-item-section>
            </q-item>

            <q-item clickable v-ripple>
              <q-item-section avatar>
                <q-icon name="star" />
              </q-item-section>

              <q-item-section @click="router.push('/about')">
                About
              </q-item-section>
            </q-item>
          </q-list>
        </q-scroll-area>

        <q-img
          class="absolute-top"
          src="https://cdn.quasar.dev/img/material.png"
          style="height: 150px"
        >
          <div class="absolute-bottom bg-transparent">
            <q-avatar size="56px" class="q-mb-sm">
              <img src="https://cdn.quasar.dev/img/boy-avatar.png" />
            </q-avatar>
            <div class="text-weight-bold">Stephen Lai</div>
            <div>@gmail.com</div>
          </div>
        </q-img>
      </q-drawer>

      <q-page-container>
        <router-view />
        <q-page-sticky position="bottom-right" :offset="[25, 25]">
          <q-btn fab icon="add" color="accent" :to="`/add`" />
        </q-page-sticky>
      </q-page-container>
    </q-layout>
  </div>
</template>

<script>
import { ref } from "vue";
import { useRoute, useRouter } from "vue-router";

export default {
  setup() {
    const route = useRoute();
    const router = useRouter();

    const drawer = ref(false);

    return {
      drawer,
      route,
      router,
    };
  },
};
</script>
