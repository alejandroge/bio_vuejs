<script setup lang="ts">
import {computed} from "vue";
import { RouterLink, RouterView, useRouter } from 'vue-router'

const router = useRouter()

const isArticlesRoute = computed(() => {
  return router.currentRoute.value.name == 'articles';
});

</script>

<template>
  <div id="main-app-container" :class="isArticlesRoute ? 'article-mode' : 'resume-mode'">
    <header :class="isArticlesRoute ? 'article-mode' : ''">
      <img alt="Profile Picture" class="logo" src="@/assets/profilePhoto.png" width="125" height="125" />
      <div class="wrapper">
        <div class="greetings">
          <h1 class="green">Alejandro Guevara</h1>
          <h2 class="green">Software Engineer</h2>
        </div>

        <nav>
          <RouterLink to="/">Resume</RouterLink>
          <RouterLink to="/articles">Articles</RouterLink>
        </nav>
      </div>
    </header>

    <RouterView :class="isArticlesRoute ? 'article-mode' : ''" />
  </div>
</template>

<style lang="scss" scoped>
@media (min-width: 1024px) {
  #main-app-container.resume-mode {
    display: grid;
    grid-template-columns: 50% 50%;
    grid-template-areas: 
    "header main";

    padding: 0 2rem;

    header {
      grid-area: header;
    }
  }
}

#main-app-container.article-mode {
  display: grid;
  grid-template-columns: 25% 25% 25% 25%;
  grid-template-areas: 
  "header header header header"
  "sidebar article article article";

  header {
    justify-content: center;

    img {
      border-radius: 50%;

      max-width: 75px;
      max-height: 75px;
    }

    .wrapper {
      display: flex;
      flex-direction: column;

      .greetings {
        h1 {
          top: 10px;
        }
      }

      nav {
        margin-top: 0px;
      }
    }
  }
}

#main-app-container {
  padding: 0 2rem;
}

header {
  line-height: 1.5;
  max-height: 100vh;
  grid-area: header;
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
    align-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  nav {
    text-align: left;
    margin-left: -1rem;
    font-size: 1rem;

    padding: 1rem 0;
    margin-top: 1rem;
  }
}

h1 {
  font-weight: 500;
  font-size: 2.6rem;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
