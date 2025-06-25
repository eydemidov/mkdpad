# Mkdpad

This is an experimental prototype of a Markdown editor that has non-intrusive AI features, that help you write in your own words but without writing for you.

Uses Vim motions in the editor.

Planned features:

* Model tuned for giving good advice on English writing and rearranging the text quickly, but *without* writing it for you.
* Ability to select desired style and writing guide.
* Ability to select between American and British English.
* Convenient keyboard-only navigation.
* Ability to save documents.

# sv

Everything you need to build a Svelte project, powered by [`sv`](https://github.com/sveltejs/cli).

## Creating a project

If you're seeing this, you've probably already done this step. Congrats!

```bash
# create a new project in the current directory
npx sv create

# create a new project in my-app
npx sv create my-app
```

## Developing

Once you've created a project and installed dependencies with `npm install` (or `pnpm install` or `yarn`), start a development server:

```bash
npm run dev

# or start the server and open the app in a new browser tab
npm run dev -- --open
```

## Building

To create a production version of your app:

```bash
npm run build
```

You can preview the production build with `npm run preview`.

> To deploy your app, you may need to install an [adapter](https://svelte.dev/docs/kit/adapters) for your target environment.
