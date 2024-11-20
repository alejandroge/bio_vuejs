<template>
  <nav class="navbar is-fixed-top" role="navigation" aria-label="main navigation">
    <div class="container">
      <div class="navbar-brand">
        <RouterLink class="navbar-item" to="/">
          <figure class="image is-24x24">
            <img src="@/assets/home-icon.svg" />
          </figure>
        </RouterLink>

        <a
          :class="{ 'navbar-burger': true, 'is-active': menuExpanded }"
          role="button"
          aria-label="menu"
          aria-expanded="false"
          @click="menuExpanded = !menuExpanded"
        >
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
        </a>
      </div>

      <div :class="{ 'navbar-menu': true, 'is-active': menuExpanded }">
        <div class="navbar-start">
          <RouterLink class="navbar-item" to="/articles">Articles</RouterLink>
          <RouterLink class="navbar-item" to="/projects">Projects</RouterLink>
        </div>
      </div>
    </div>
  </nav>

  <RouterView />
</template>

<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'
</script>

<script lang="ts">
export default {
  data(): { menuExpanded: boolean } {
    return {
      menuExpanded: false
    }
  }
}
</script>
