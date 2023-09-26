<template>
    <div class="app_layout">
      <app-layout-header />
    </div>
  </template>
  
  <script setup>
  import AppLayoutHeader from "./AppHeader.vue";
  </script>
  
  <style lang="scss" scoped>
  .app_layout {
    display: flex;
    flex-direction: column;
    height: 100vh;
  }
  </style>