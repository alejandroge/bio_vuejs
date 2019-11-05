<template>
  <v-app>
    <v-app-bar app :dark="darkMode">
      <v-spacer></v-spacer>
      <v-toolbar-title centered class="headline text-uppercase">
        <span>Alejandro Guevara</span>
        <span class="ml-5 font-weight-light">Web Developer</span>
      </v-toolbar-title>

      <v-spacer></v-spacer>
      <v-btn icon @click="darkMode = !darkMode">
        <v-icon>mdi-theme-light-dark</v-icon>
      </v-btn>

      <template v-slot:extension>
        <v-tabs
          v-model="tab"
          centered
          >
          <v-tab
            v-for="item in items"
            :key="item"
            >
            {{ item }}
          </v-tab>
        </v-tabs>
      </template>
    </v-app-bar>

    <v-content>
      <v-tabs-items v-model="tab">
        <v-tab-item
          v-for="item in items"
          :key="item"
          >
          <v-card flat>
            <v-card-text>{{ text }}</v-card-text>
          </v-card>
        </v-tab-item>
      </v-tabs-items>
    </v-content>
  </v-app>
</template>

<script>
export default {
  name: 'App',
  data: () => ({
    darkMode: false,
    tab: null,
    items: ['Me', 'Portfolio', 'Blog'],
    text: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.',
  }),
};
</script>
