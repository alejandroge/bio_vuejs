<script setup lang="ts">
import { ref, computed } from 'vue';

import Article from '../components/Article.vue'

import axios from 'axios';

type SupportedLanguages = 'en' | 'es';

type Article = {
  title: string,
  published: string,
  fileName: string,
  lang: SupportedLanguages,
}

const articles: Article[] = [
  {
    title: 'Ruby "tap" method analysis',
    published: 'Published on July 12, 2020',
    fileName: 'rubyTapMethodAnalysis',
    lang: 'en',
  },
  {
    title: 'CSRF en Rails',
    published: 'Publicado el 3 de Julio de 2020',
    fileName: 'csrfRails',
    lang: 'es',
  },
]

type Section = {
  title: string,
  articles: Article[],
}

const sidebarSections: Section[] = [
  {
    title: 'English articles',
    articles: articles.filter(article => article.lang == 'en'),
  },
  {
    title: 'Articulos en Español',
    articles: articles.filter(article => article.lang == 'es'),
  }
]

const loading = ref(false);
const selectedArticle = ref(articles[0]);
const selectedArticleText = ref('');

const fetchArticle = async (article: Article) => {
  const articlesBasePath = './articles_files/';

  return axios.get(`${articlesBasePath}${article.fileName}.md`)
    .then(response => selectedArticleText.value = response.data);
}

async function selectArticle(article: Article) {
  selectedArticle.value = article;

  loading.value = true;
  await fetchArticle(selectedArticle.value);
return loading.value = false;
}

const englishArticles = computed(() => {
  return articles.filter(article => article.lang == 'en');
});

const spanishArticles = computed(() => {
  return articles.filter(article => article.lang == 'es');
});

fetchArticle(selectedArticle.value);
</script>

<template>
  <aside>
    <div
        v-for="section, sectionIndex in sidebarSections"
        :key="sectionIndex"
        class="aside-section"
    >
      <p class="section-title">{{ section.title }}</p>

      <a
        v-for="(article, articleIndex) in section.articles"
        :key="articleIndex"
        class="article-navigation-link"
        @click="selectArticle(article)"
      >
        <p class="article-title">{{ article.title }}</p>
        <p class="article-published-at">{{ article.published }}</p>
      </a>
    </div>
  </aside>

  <main>
    <Article v-if="!loading" :article-text="selectedArticleText" />
  </main>
</template>

<style scoped lang="scss">
aside {
  grid-area: sidebar;
  color: hsla(160, 100%, 37%, 1);
  margin: 1.414rem 0 .5rem;

  .aside-section {
    margin-bottom: 20px;

    .section-title {
      font-size: 1.3rem;
    }

    .article-navigation-link {
      cursor: pointer;

      .article-title {
        font-size: 1.1rem;
        filter: brightness(60%);
      }

      .article-published-at {
        font-size: 0.8rem;
      }
    }
  }
}

main {
  grid-area: article;
}

</style>