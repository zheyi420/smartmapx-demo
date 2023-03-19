# smartmapx-demo

This template should help get you started developing with Vue 3 in Vite.

- Module Bundler: [Vite 4](https://vitejs.dev/)
- JavaScript Framework for Web User Interfaces: [Vue 3](https://vuejs.org/)
- Store Library for Vue: [Pinia 2](https://pinia.vuejs.org/)
- Router: [Vue Router 4](https://router.vuejs.org/)
- JavaScript library for maps: [SmartMapX JS](https://www.smartmapx.com/docs/apidoc/jsdoc/api/)
- JavaScript library for spatial analysis: [Turf.js](https://turfjs.org/)

## Function Demonstration

- Spatial Relationships
- Measurement Functions
- Route Planning
- Path Playback


## Recommended IDE Setup

[VSCode](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur) + [TypeScript Vue Plugin (Volar)](https://marketplace.visualstudio.com/items?itemName=Vue.vscode-typescript-vue-plugin).


## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```

## About 'VITE_BUILD_PATH_PREFIX'

The files `.env.development` and `.env.production` are configs for development and production environment.

Since it is deployed on GitHub Pages `https://<USERNAME>.github.io/<REPO>/`, you need to set `VITE_BUILD_PATH_PREFIX='<REPO>'` to refer to the static files in `/public`.

In general or dev env, just set `VITE_BUILD_PATH_PREFIX=''`, it should be no problem.
