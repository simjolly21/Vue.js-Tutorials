# Quick Start

## Creating a Vue Application

### Prerequisites
- Familiarity with the command line
- Install Node.js version 18.3 or higher

In this section we will introduce how to scaffold a Vue Single Page Application on your local machine. The created project will be using a build setup based on Vite and allow us to use Vue Single-File Components (SFCs).

Make sure you have an up-to-date version of Node.js installed and your current working directory is the one where you intend to create a project. Run the following command in your command line (without the `$` sign):

- npm: `npm create vue@latest`
- pnpm: `pnpm create vue@latest`
- yarn: 

        # For Yarn (v1+)
        yarn create vue

        # For Yarn Modern (v2+)
        yarn create vue@latest

        # For Yarn ^v4.11
        yarn dlx create-vue@latest

- bun: `bun create vue@latest`

This command will install and execute [create-vue](https://github.com/vuejs/create-vue), the official Vue project scaffolding tool. You will be presented with prompts for several optional features such as TypeScript and testing support:

    ✔ Project name: … <your-project-name>
    ✔ Add TypeScript? … No / Yes
    ✔ Add JSX Support? … No / Yes
    ✔ Add Vue Router for Single Page Application development? … No / Yes
    ✔ Add Pinia for state management? … No / Yes
    ✔ Add Vitest for Unit testing? … No / Yes
    ✔ Add an End-to-End Testing Solution? … No / Cypress / Nightwatch / Playwright
    ✔ Add ESLint for code quality? … No / Yes
    ✔ Add Prettier for code formatting? … No / Yes
    ✔ Add Vue DevTools 7 extension for debugging? (experimental) … No / Yes

    Scaffolding project in ./<your-project-name>...
    Done.

If you are unsure about an option, simply choose `No` by hitting enter for now. Once the project is created, follow the instructions to install dependencies and start the dev server:

- npm:

        cd <your-project-name>
        npm install
        npm run dev

- pnpm:

        cd <your-project-name>
        pnpm install
        pnpm run dev

- yarn: 

        cd <your-project-name>
        yarn
        yarn dev

- bun: 

        cd <your-project-name>
        bun install
        bun run dev

You should now have your first Vue project running! Note that the example components in the generated project are written using the [Composition API](https://vuejs.org/guide/introduction.html#composition-api) and `<script setup>`, rather than the [Options API](https://vuejs.org/guide/introduction.html#options-api).

When you are ready to ship your app to production, run the following:

- npm: `npm run build`
- pnpm: `pnpm run build`
- yarn: `yarn run build`
- bun: `bun run build`

This will create a production-ready build of your app in the project's `./dist` directory. Check out the Production Deployment Guide to learn more about shipping your app to production.