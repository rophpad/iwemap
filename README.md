# Iwemap

Open-source online explorer for physical library books.

Live: [iwemap.vercel.app](https://iwemap.vercel.app)

## What it does

Iwemap makes the physical catalog of a library discoverable online. Visitors can browse available books, search, and explore participating libraries — close to borrowing a real book.

## Features

- **Browse books** across a library's collection
- **Search** with result pages and filters
- **Library explorer** — locations and details for participating libraries
- **Borrow flow** — select books you want to check out
- **Multi-language selector** and pagination
- **Library onboarding** — add a new library to the platform

## Tech stack

- [Vue 3](https://vuejs.org) + [Vite](https://vite.dev)
- [Pinia](https://pinia.vuejs.org) state management
- [Vue Router](https://router.vuejs.org)
- Tailwind CSS
- Unit tests with [Vitest](https://vitest.dev)

## Getting started

```sh
pnpm install
pnpm dev
```

See the [Vite configuration reference](https://vite.dev/config/) for build settings. Run the test suite with `pnpm test:unit`.

## Project structure

- `src/pages` — HomePage, BooksPage, BookPage, LibrariesPage, SearchResultsPage
- `src/components` — BookCard, FilterDropdown, LanguageSelector, modals (AddLibrary, BorrowBooks, LibraryLocation, ProfileForm)
- `src/data` — `libraries.ts` and `all.ts` seed data
- `src/stores` — Pinia stores for search and modals

## Status

Active open-source project. Default Vue/Vite template README replaced on 2026-09; feature set reflects the current codebase.