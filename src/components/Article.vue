<template>
  <div class="article" v-html="articleRawHtml"></div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';
import axios from 'axios';
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

const articleText = ref('');
const props = defineProps({
  articleName: String
})
const articleRawHtml = computed(() => {
  return md.render(articleText.value);
});

const articlesBasePath = './articles/';
const axiosArticle = async () => {
  axios.get(`${articlesBasePath}${props.articleName}`)
    .then(response => articleText.value = response.data);
}

axiosArticle();
</script>

<style lang="scss">
@import 'highlight.js/styles/default.css';

.article {
  // all styles comming from
  // https://github.com/markdowncss/air/blob/master/css/air.css
  @media print {
    *,
    *:before,
    *:after {
      background: transparent !important;
      color: #000 !important;
      box-shadow: none !important;
      text-shadow: none !important;
    }

    a,
    a:visited {
      text-decoration: underline;
    }

    a[href]:after {
      content: " (" attr(href) ")";
    }

    abbr[title]:after {
      content: " (" attr(title) ")";
    }

    a[href^="#"]:after,
    a[href^="javascript:"]:after {
      content: "";
    }

    pre,
    blockquote {
      border: 1px solid #999;
      page-break-inside: avoid;
    }

    thead {
      display: table-header-group;
    }

    tr,
    img {
      page-break-inside: avoid;
    }

    img {
      max-width: 100% !important;
    }

    p,
    h2,
    h3 {
      orphans: 3;
      widows: 3;
    }

    h2,
    h3 {
      page-break-after: avoid;
    }
  }

  html {
    font-size: 12px;
  }

  @media screen and (min-width: 32rem) and (max-width: 48rem) {
    html {
      font-size: 15px;
    }
  }

  @media screen and (min-width: 48rem) {
    html {
      font-size: 16px;
    }
  }

  body {
    line-height: 1.85;
  }

  p,
  .air-p {
    font-size: 1rem;
    margin-bottom: 1.3rem;
  }

  h1,
  .air-h1,
  h2,
  .air-h2,
  h3,
  .air-h3,
  h4,
  .air-h4 {
    margin: 1.414rem 0 .5rem;
    font-weight: inherit;
    line-height: 1.42;
  }

  h1,
  .air-h1 {
    margin-top: 0;
    font-size: 3.998rem;
  }

  h2,
  .air-h2 {
    font-size: 2.827rem;
  }

  h3,
  .air-h3 {
    font-size: 1.999rem;
  }

  h4,
  .air-h4 {
    font-size: 1.414rem;
  }

  h5,
  .air-h5 {
    font-size: 1.121rem;
  }

  h6,
  .air-h6 {
    font-size: .88rem;
  }

  small,
  .air-small {
    font-size: .707em;
  }

  /* https://github.com/mrmrs/fluidity */

  img,
  canvas,
  iframe,
  video,
  svg,
  select,
  textarea {
    max-width: 100%;
  }


  body {
    color: #444;
    font-weight: 300;
    margin: 6rem auto 1rem;
    max-width: 48rem;
    text-align: center;
  }

  img {
    border-radius: 50%;
    height: 200px;
    margin: 0 auto;
    width: 200px;
  }

  a,
  a:visited {
    color: #3498db;
  }

  a:hover,
  a:focus,
  a:active {
    color: #2980b9;
  }

  pre {
    background-color: #fafafa;
    padding: 1rem;
    text-align: left;
  }

  blockquote {
    margin: 0;
    border-left: 5px solid #7a7a7a;
    font-style: italic;
    padding: 1.33em;
    text-align: left;
  }

  ul,
  ol,
  li {
    text-align: left;
  }

  p {
    color: #777;
  }
}

</style>

<style lang="scss" scoped>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }

  .article {
    overflow-y: auto;
  }
}

.article {
  max-height: 100vh;
  padding-right: 20px;

  :deep(h1), :deep(h2), :deep(h3), :deep(h4), :deep(h5), :deep(h6) {
    color: hsla(160, 100%, 37%, 1);
  }
}
</style>