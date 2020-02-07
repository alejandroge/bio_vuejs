<template>
  <div class="progress-group" :class="kebabCase(label)">
    <v-progress-circular
      :rotate="90"
      :value="percent"
      :size="132"
      :color="color"
      >
      <slot name="icon"></slot>
    </v-progress-circular>
    <span>{{ label }}</span>
  </div>
</template>

<script>
export default {
  name: 'Progress',
  props: {
    percent: {
      type: Number,
      required: true,
    },
    label: {
      type: String,
      required: true,
    },
    color: {
      type: String,
      default: 'green',
    }
  },
  methods: {
    kebabCase: function(str) {
      return str &&
        str.match(/[A-Z]{2,}(?=[A-Z][a-z]+[0-9]*|\b)|[A-Z]?[a-z]+[0-9]*|[A-Z]|[0-9]+/g)
        .map(x => x.toLowerCase())
        .join('-')
    }
  },
}
</script>

<style lang="scss">
  .progress-group {
    margin: 10px;
    display: inline-block;

    img {
      width: 80px;
    }

    span {
      display: block;
      text-align: center;
    }
  }

</style>
