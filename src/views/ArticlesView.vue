<template>
  <div class="container mt-2">
    <div class="columns">
      <div class="column is-four-fifths">
        <Article v-if="!loading" :article-text="selectedArticleText" />
      </div>
      <div class="column">
        <aside class="menu">
          <template
            v-for="(section, sectionIndex) in sidebarSections"
            :key="`menu-section-${sectionIndex}`"
          >
            <p class="menu-label">{{ section.title }}</p>
            <ul class="menu-list">
              <li
                v-for="(article, articleIndex) in section.articles"
                :key="`article-${sectionIndex}-${articleIndex}`"
              >
                <a :key="articleIndex" @click="selectArticle(article)">
                  <p class="article-title">{{ article.title }}</p>
                  <p class="article-published-at">{{ article.published }}</p>
                </a>
              </li>
            </ul>
          </template>
        </aside>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

import Article from '../components/Article.vue'

import axios from 'axios'

type SupportedLanguages = 'en' | 'es'

type ArticleType = {
  title: string
  published: string
  fileName: string
  lang: SupportedLanguages
}

const articles: ArticleType[] = [
  {
    title: 'Ruby "tap" method analysis',
    published: 'Published on July 12, 2020',
    fileName: 'rubyTapMethodAnalysis',
    lang: 'en'
  },
  {
    title: 'CSRF en Rails',
    published: 'Publicado el 3 de Julio de 2020',
    fileName: 'csrfRails',
    lang: 'es'
  }
]

type Section = {
  title: string
  articles: ArticleType[]
}

const sidebarSections: Section[] = [
  {
    title: 'English articles',
    articles: articles.filter((article) => article.lang == 'en')
  },
  {
    title: 'Artículos en Español',
    articles: articles.filter((article) => article.lang == 'es')
  }
]

const loading = ref(false)
const selectedArticle = ref(articles[0])
const selectedArticleText = ref('')

const fetchArticle = async (article: ArticleType) => {
  const articlesBasePath = './articles_files/'

  return axios
    .get(`${articlesBasePath}${article.fileName}.md`)
    .then((response) => (selectedArticleText.value = response.data))
}

async function selectArticle(article: ArticleType) {
  selectedArticle.value = article

  loading.value = true
  await fetchArticle(selectedArticle.value)
  return (loading.value = false)
}

const englishArticles = computed(() => {
  return articles.filter((article) => article.lang == 'en')
})

const spanishArticles = computed(() => {
  return articles.filter((article) => article.lang == 'es')
})

fetchArticle(selectedArticle.value)
</script>
