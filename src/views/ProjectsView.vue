<template>
  <div class="container mt-2">
    <section class="hero is-small">
      <div class="hero-body">
        <p class="title">Projects</p>
        <p class="subtitle">
          Some projects that I've worked on that can be explored either live and/or on Github.
        </p>
      </div>
    </section>

    <div class="grid is-col-min-15">
      <div v-for="(project, idx) in projects" class="cell" :key="`project-${idx}`">
        <div class="card">
          <div class="card-image">
            <figure class="image is-4by3">
              <img :src="project.imageFilePath" :alt="`${project.title} image`" />
            </figure>
          </div>
          <div class="card-content">
            <p
              v-for="(paragraph, index) in project.paragraphs"
              :key="`paragraph-${index}`"
              class="mb-2"
            >
              {{ paragraph }}
            </p>
          </div>
          <footer class="card-footer">
            <p v-if="project.webpageLink" class="card-footer-item">
              <span> Project is live <a :href="project.webpageLink" target="_blank">here</a> </span>
            </p>
            <p v-if="project.githubLink" class="card-footer-item">
              <span> View on <a :href="project.githubLink" target="_blank">Github</a> </span>
            </p>
          </footer>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
type ProjectType = {
  title: string
  imageFilePath: string
  lang: string
  githubLink: string
  paragraphs: string[]
}

const projects: ProjectType[] = [
  {
    title: 'GlabToggl spoon',
    imageFilePath: './projects_images/GlabTogglSpoon-4by3.png',
    lang: 'en',
    githubLink: 'https://github.com/alejandroge/GlabToggl.spoon',
    paragraphs: [
      'A Hammerspoon spoon that integrates Gitlab and Toggl Track. It allows you to start/stop Toggl timers from Gitlab issues assigned to you.',
      'The project is written in Lua, and uses the Hammerspoon UI (only available for MacOS).'
    ]
  },
  {
    title: 'Internet monitor',
    imageFilePath: './projects_images/internetMonitor-4by3.png',
    lang: 'en',
    githubLink: 'https://github.com/alejandroge/internet-monitor',
    paragraphs: [
      'A disconnections monitor. A minimal web page that shows the recent logs and your local network.',
      'It is a Ruby script that analyzes the results of the ping command, executed by a cron task. A minimal Go api exposes the information to a VueJS frontend.'
    ]
  },
  {
    title: 'My personal website',
    imageFilePath: './projects_images/bio-4by3.png',
    lang: 'en',
    webpageLink: 'https://aguevara.me/',
    githubLink: 'https://github.com/alejandroge/bio_vuejs',
    paragraphs: [
      'My public portfolio to display with short articles and my side projects (The website that you are on right now!).',
      'I used VueJS and BulmaCSS. The project auto deploys to Github pages using Github Actions.'
    ]
  },
  {
    title: 'Browser mini video game',
    imageFilePath: './projects_images/ritchie-4by3.png',
    lang: 'en',
    webpageLink: 'https://alejandroge.github.io/ritchie/',
    githubLink: 'https://github.com/alejandroge/ritchie',
    paragraphs: [
      'A small video game, that runs entirely on the browser. I designed the graphics for it (from which you can see, that I am no designer), and three different levels.',
      'The project is written only with Javascript, using KineticJS library.'
    ]
  },
  {
    title: 'A generic company website',
    imageFilePath: './projects_images/company-4by3.png',
    lang: 'en',
    webpageLink: 'https://alejandroge.github.io/companystd/',
    githubLink: 'https://github.com/alejandroge/companystd',
    paragraphs: [
      'A generic company webpage that I designed a few years back. It mostly features a couple different sections, and forms',
      'There is no functionality on the webpage, it is just HTML and CSS.'
    ]
  }
]

export default {
  data(): { projects: ProjectType[] } {
    return {
      projects
    }
  }
}
</script>
