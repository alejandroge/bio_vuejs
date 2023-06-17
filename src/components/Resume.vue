<script setup lang="ts">
import ResumeItem from './ResumeItem.vue'
import SchoolIcon from './icons/IconSchool.vue'
import JobIcon from './icons/IconJob.vue'
</script>

<template>
  <ResumeItem>
    <template #icon>
      <JobIcon />
    </template>
    <template #heading>
      <a href="https://www.linkedin.com/company/demodesk/" target="_blank" rel="noopener noreferrer">
        Demodesk
      </a>
    </template>

    <p>Senior Software Engineer, 2023 - now</p>
    <p>Mid Software Engineer, 2021 - 2023</p>
    <p class="tech-list">
      <span class="tech-list-item">Ruby on Rails</span>
      <span class="tech-list-item">Vue JS</span>
    </p>
  </ResumeItem>

  <ResumeItem>
    <template #icon>
      <JobIcon />
    </template>
    <template #heading>
      <a href="https://www.linkedin.com/company/thincode/" target="_blank" rel="noopener noreferrer">
        Thincode
      </a>
    </template>

    <p>Software Developer, 2017 - 2021</p>
    <p class="tech-list">
      <span class="tech-list-item">Ruby on Rails</span>
      <span class="tech-list-item">Vue JS</span>
    </p>
  </ResumeItem>

  <ResumeItem>
    <template #icon>
      <SchoolIcon />
    </template>
    <template #heading>
      <a href="https://www.ugto.mx/" target="_blank" rel="noopener noreferrer">
        Universidad de Guanajuato
      </a>
    </template>

    <p>Computer Systems Engineer, 2013 - 2018</p>
  </ResumeItem>
</template>

<style scoped>
.tech-list-item {
  padding-right: 10px;
  color: gray;
}
</style>
