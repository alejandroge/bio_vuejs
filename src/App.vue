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
      <v-tabs-items :dark="darkMode" v-model="tab">
        <v-tab-item>
          <Resume/>
        </v-tab-item>
        <v-tab-item>
          <Resume
            :darkMode="darkMode"/>
        </v-tab-item>
        <v-tab-item>
          <Resume/>
        </v-tab-item>
      </v-tabs-items>
    </v-content>
  </v-app>
</template>

<script>
import Resume from './components/Resume.vue'

export default {
  name: 'App',
  components: {
    Resume,
  },
  data: () => ({
    darkMode: false,
    tab: null,
    items: ['Portfolio', 'Resume', 'Blog'],
  }),
};
</script>
