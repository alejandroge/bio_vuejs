# bio_vuejs

## Customize configuration

See [Vite Configuration Reference](https://vitejs.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Type-Check, Compile and Minify for Production

```sh
npm run build
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```

### Deploy to aguevara.me

Simply build this project here, and copy the `dist/` contents to the repo: https://github.com/alejandroge/bio_vuejs_deployment

If I wanted to get fancy I could potentially automate this. First idea:

- Simply create a bash script that I can call, that does the building and copying. If I run this locally, then I don't need to do anything fancy either for creating a new commit and pushing it to the repo.