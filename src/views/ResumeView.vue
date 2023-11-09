<script setup lang="ts">
import Resume from '../components/Resume.vue'
</script>

<template>
  <main>
    <Resume />
  </main>
</template>

<style scoped>
aside {
  grid-area: sidebar;
}

main {
  grid-area: main;
}
</style>
