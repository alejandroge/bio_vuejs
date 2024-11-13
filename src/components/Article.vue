<template>
  <div class="content" v-html="articleRawHtml"></div>
</template>

<script setup lang="ts">
import { computed, toRefs } from 'vue';

import hljs from 'highlight.js/lib/core';
import ruby from 'highlight.js/lib/languages/ruby';
import MarkdownIt from 'markdown-it';

hljs.registerLanguage('ruby', ruby);

const md = new MarkdownIt({
  html: true,
  linkify: true,
  typographer: true,
  highlight: function (str: string, lang: string) {
    if (lang && hljs.getLanguage(lang)) {
      try {
        return hljs.highlight(str, { language: lang }).value;
      } catch (__) { }
    }

    return ''; // use external default escaping
  }
})

export interface Props {
  articleText: string
}
const props = withDefaults(
  defineProps<Props>(),
  {
    articleText: ''
  }
)

const { articleText } = toRefs(props);

const articleRawHtml = computed(() => {
  return md.render(articleText.value);
});
</script>

<style lang="scss">
@import 'highlight.js/styles/default.css';
</style>

