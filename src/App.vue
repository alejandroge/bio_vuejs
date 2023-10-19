<script setup lang="ts">
import {computed} from "vue";
import { RouterLink, RouterView, useRouter } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'

const router = useRouter()

const isArticlesRoute = computed(() => {
  return router.currentRoute.value.name == 'articles';
});

</script>

<template>
  <header :class="isArticlesRoute ? 'article-mode' : ''">
    <img alt="Profile Picture" class="logo" src="@/assets/profilePhoto.png" width="125" height="125" />
    <div class="wrapper">
      <HelloWorld />

      <nav>
        <RouterLink to="/">Resume</RouterLink>
        <RouterLink to="/articles">Articles</RouterLink>
      </nav>
    </div>
  </header>

  <RouterView :class="isArticlesRoute ? 'extended-mode' : ''" />
</template>

<style scoped>
header {
  line-height: 1.5;
  max-height: 100vh;
  grid-column-start: 1;
  grid-column-end: 3;
}

header.article-mode {
  grid-column-start: 1;
  grid-column-end: 2;
}

main {
  grid-column-start: 3;
  grid-column-end: 5;
}

main.extended-mode {
  grid-column-start: 2;
  grid-column-end: 5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
  border-radius: 50%;
}

nav {
  width: 100%;
  font-size: 12px;
  text-align: center;
  margin-top: 2rem;
}

nav a.router-link-exact-active {
  color: var(--color-text);
}

nav a.router-link-exact-active:hover {
  background-color: transparent;
}

nav a {
  display: inline-block;
  padding: 0 1rem;
  border-left: 1px solid var(--color-border);
}

nav a:first-of-type {
  border: 0;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  nav {
    text-align: left;
    margin-left: -1rem;
    font-size: 1rem;

    padding: 1rem 0;
    margin-top: 1rem;
  }
}
</style>
