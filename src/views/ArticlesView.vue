<script setup lang="ts">
import Article from '../components/Article.vue'

const articles = [
  {
    title: 'Ruby "tap" method analysis',
    published: '',
    fileName: 'rubyTapMethodAnalysis',
    lang: 'en',
  },
  {
    title: 'CSRF en Rails',
    published: '',
    fileName: 'csrfRails',
    lang: 'es',
  },
]
</script>

<template>
  <main>
    <Article v-for="article in articles" :article-name="article.fileName" />
  </main>
</template>