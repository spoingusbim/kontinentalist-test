# Lauryn's Submission

## Intro

This is a Typescript project running on Sveltekit. Both assignment sections are placed within the same page, with Section A at the top, and Section B at the bottom.

## Run the project

1. Clone the repo and run `pnpm install` or `npm install` to install all dependencies and generate node_modules using pnpm / npm.
2. run `pnpm dev` or `npm dev` to launch local development.
3. You may also visit https://kontinentalist-test-lauryn.netlify.app/ to view deployed site

## Folder Orientation

There is only one page, which will be found in `src/routes/+page.svelte`. The page renders the two main components given in the test. The components can be found in `src/lib/components`, while the data can be found in `src/lib/assets`. Generic styles are found in `src/layout.css` which uses TailwindCSS. Component-specific and page-specific styles are found within the individual .svelte files themselves.
