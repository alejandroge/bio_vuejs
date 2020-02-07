<template>
  <v-card-text>
    <v-container >
      <v-row no-gutters>
        <h3>About me</h3>
        <div>
          I'm a Fullstack web developer. I love programming and creating new applications. The main
          technologies that I've got experience working with are Ruby, Ruby on Rails, VueJS,
          Postgres, etc.

          I've been part of SCRUM and Waterfall teams. I'm a certified SCRUM decveloper.

          I also love music, I play guitar and keyboard.
        </div>
      </v-row>
      <v-row>
        <v-col>
          <h3>Technologies</h3>
          <Progress
            :percent="80"
            color="red"
            label="Ruby on Rails">
            <template slot="icon">
              <img src="https://upload.wikimedia.org/wikipedia/commons/6/62/Ruby_On_Rails_Logo.svg">
            </template>
          </Progress>
          <Progress
            :percent="70"
            label="VueJS">
            <template slot="icon">
              <img src="https://upload.wikimedia.org/wikipedia/commons/9/95/Vue.js_Logo_2.svg">
            </template>
          </Progress>
        </v-col>
        <v-col>
          <h3>Work experience</h3>
        </v-col>
      </v-row>
    </v-container>
  </v-card-text>
</template>

<script>
import Progress from './Progress.vue'

export default {
  name: 'Resume',
  components: {
    Progress,
  },
  props: {
    darkMode: {
      type: Boolean,
      default: () => false,
    }
  }
}
</script>
