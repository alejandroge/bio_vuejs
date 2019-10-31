<template>
  <v-app>
    <v-app-bar app :dark="darkMode">
      <v-toolbar-title class="headline text-uppercase">
        <span>Alejandro Guevara</span>
        <span class="font-weight-light">Web Developer</span>
      </v-toolbar-title>

      <v-spacer></v-spacer>
      <v-btn icon @click="darkMode = !darkMode">
        <v-icon>mdi-theme-light-dark</v-icon>
      </v-btn>
    </v-app-bar>

    <v-content>
    </v-content>
  </v-app>
</template>

<script>
export default {
  name: 'App',
  data: () => ({
    darkMode: false,
  }),
};
</script>
