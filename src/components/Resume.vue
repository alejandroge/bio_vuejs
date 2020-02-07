<template>
  <v-card-text>
    <div>Hello there!</div>
  </v-card-text>
</template>

<script>
export default {
  name: 'Resume',
  props: {
    darkMode: {
      type: Boolean,
      default: () => false,
    }
  }
}
</script>
