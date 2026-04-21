# Article Hub — Frontend

> **Read in:** [Русский](./README.ru.md)

A production-ready single-page application for reading, writing, and discussing articles. Built with React 18, TypeScript, Redux Toolkit and structured with the Feature Sliced Design (FSD) architecture.

---

## Table of Contents

- [Overview](#overview)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [Features](#features)
- [Getting Started](#getting-started)
- [Scripts](#scripts)
- [Testing](#testing)
- [CI/CD](#cicd)
- [Project Structure](#project-structure)
- [What This Project Demonstrates](#what-this-project-demonstrates)

---

## Overview

Article Hub is a full-featured content platform where users can:

- Browse and read articles with rich text, code snippet, and image blocks
- Write and edit their own articles
- Rate articles with a star-based system
- Leave and read comments
- Follow article recommendations
- Manage their own user profile
- Switch between light and dark themes
- Use the interface in English or Russian

The application includes an admin panel for users with elevated roles (MANAGER / ADMIN), protected routing with role-based access control, and a dual design system controlled by a runtime feature flag to progressively roll out the redesigned UI.

---

## Tech Stack

| Category | Technologies |
|---|---|
| **Core** | React 18, TypeScript 4.5 |
| **Routing** | React Router v6 |
| **State management** | Redux Toolkit, RTK Query |
| **Build tools** | Webpack 5 (primary), Vite 3 (alternative) |
| **Styling** | SCSS Modules, BEM naming convention |
| **Internationalization** | i18next, react-i18next, language detection |
| **Animation** | React Spring, use-gesture |
| **Unit testing** | Jest 27, React Testing Library |
| **E2E testing** | Cypress 11 |
| **Visual regression** | Loki |
| **Component docs** | Storybook 6 |
| **Code quality** | ESLint (Airbnb + FSD plugin), Stylelint, Prettier |
| **Pre-commit hooks** | Husky + lint-staged |
| **CI/CD** | GitHub Actions |
| **Mock backend** | json-server |

---

## Architecture

The project strictly follows **Feature Sliced Design (FSD)** — a scalable, modular frontend architecture that organises code by business domain and enforces strict boundaries between layers.

```
src/
├── app/          # Application bootstrap (providers, global styles, routing)
├── pages/        # Route-level page components
├── widgets/      # Large, self-contained UI blocks (Navbar, Sidebar, Filters…)
├── features/     # User-facing functionality (login, rating, comments…)
├── entities/     # Business domain models (User, Article, Comment…)
└── shared/       # Reusable utilities, UI kit, hooks, API client
```

### Import direction (downward only)

```
app → pages → widgets → features → entities → shared
```

No layer may import from a layer above it, which keeps every module isolated, independently testable, and replaceable without side effects.

### Key architectural decisions

**Dynamic reducer injection** — Redux slices are loaded on demand via `DynamicModuleLoader`. Only the reducers the current page needs are in memory, keeping the initial bundle lean.

**Feature flags** — The `toggleFeatures({ name, on, off })` utility enables gradual rollout of new functionality without branching or duplicating routes. The `isAppRedesigned` flag switches the entire design system at runtime.

**Dual design system** — A `deprecated/` UI kit (original design) coexists with a `redesigned/` kit in `shared/ui/`. The feature flag decides which one renders — no user sees a half-migrated UI.

**Code splitting** — Every page is lazy-loaded via `React.lazy()` following the `.async.tsx` naming convention. Reducers follow the same on-demand pattern.

**Public API exports** — Each FSD module exposes only what it intends to share through its `index.ts`. Internal implementation details are not importable from the outside.

---

## Features

### Authentication & Authorization

- Username / password login form with loading and error states
- Token stored in `localStorage`, automatically injected into every request via an Axios interceptor and RTK Query's `prepareHeaders`
- `RequireAuth` component guards all protected routes at the router level
- Role-based access: regular users, MANAGER, ADMIN
- Session restored automatically from `localStorage` on every app load (`initAuthData` thunk)

### Article System

- Paginated article list with infinite scroll (Intersection Observer)
- Filter by article type: IT, Economics, Science, All
- Sort by date, views, or rating; toggle ascending / descending order
- Two display modes: card grid and compact list
- Article detail page with three block types: **Text**, **Code** (syntax highlighted), **Image**
- Create and edit articles with a block-based editor (authenticated users only)
- Article recommendations section powered by related content

### Social & Engagement

- 1–5 star rating on articles with feedback text
- Comment section with inline form and instant optimistic update
- User profile page: avatar, full name, city, country, currency, age
- Editable profile card with validation
- Notification bell with unread count badge

### Admin Panel

- Accessible only to users with MANAGER or ADMIN role, enforced by both the route guard and the backend
- Provides system-level content management UI

### UX & Accessibility

- Light / Dark theme toggle, choice persisted in `localStorage`
- Language switch (Russian ↔ English) with automatic browser language detection on first visit
- Scroll-to-top button that appears after scrolling down
- Scroll position preserved when navigating back to a list page
- Sticky sidebar and filter toolbar on article listing
- Responsive layouts built with `StickyContentLayout` and `MainLayout` components

---

## Getting Started

### Prerequisites

- Node.js ≥ 16
- npm ≥ 7

### Install dependencies

```bash
npm install
```

### Start the mock API server

The backend is simulated with json-server on port **8000** with an 800 ms artificial delay to replicate real network conditions.

```bash
npm run start:dev:server
```

### Start the development server

**Webpack** (default, port 3000):
```bash
npm run start:dev
```

**Vite** (faster cold start):
```bash
npm run start:dev:vite
```

Open [http://localhost:3000](http://localhost:3000) in your browser.

### Test credentials

Any user from `/json-server/db.json` can be used. Example:
```
username: testuser
password: 123
```

---

## Scripts

### Development

| Command | Description |
|---|---|
| `npm run start` | Start Webpack dev server (port 3000) |
| `npm run start:vite` | Start Vite dev server |
| `npm run start:dev` | Webpack + json-server |
| `npm run start:dev:vite` | Vite + json-server |
| `npm run start:dev:server` | json-server only |

### Build

| Command | Description |
|---|---|
| `npm run build:prod` | Production build (Webpack, minified) |
| `npm run build:dev` | Development build (no minification) |
| `npm run analyze` | Open Webpack Bundle Analyzer |

### Code quality

| Command | Description |
|---|---|
| `npm run lint:ts` | Lint TypeScript / TSX files |
| `npm run lint:ts:fix` | Auto-fix TypeScript lint issues |
| `npm run lint:scss` | Lint SCSS files |
| `npm run lint:scss:fix` | Auto-fix SCSS lint issues |
| `npm run prettier` | Format all files with Prettier |

### Testing

| Command | Description |
|---|---|
| `npm run test:unit` | Run Jest unit tests |
| `npm run test:e2e` | Open Cypress interactive runner |
| `npm run test:ui` | Run Loki visual regression tests |
| `npm run test:ui:ok` | Approve new Loki reference screenshots |
| `npm run test:ui:ci` | Loki in strict CI mode |
| `npm run test:ui:report` | Generate visual test HTML report |

### Documentation & utilities

| Command | Description |
|---|---|
| `npm run storybook` | Start Storybook (port 6006) |
| `npm run storybook:build` | Build static Storybook |
| `npm run generate:slice` | Scaffold a new FSD slice |
| `npm run remove-feature` | Remove a feature flag from codebase |

---

## Testing

The project has **three complementary layers of testing**:

### Unit tests — Jest + React Testing Library

Tests live alongside source files (`Component.test.tsx`). SCSS modules and SVGs are mocked. Module aliases (`@/`) resolve automatically. The `@testing-library/jest-dom` matchers are globally available. An HTML report is generated in `reports/` after each run.

```bash
npm run test:unit
```

### End-to-end tests — Cypress

Full user-journey tests in `/cypress/e2e/` cover authentication, article browsing, article creation, profile editing, and more. Reusable commands and fixtures live in `/cypress/support/`.

```bash
npm run test:e2e
```

### Visual regression — Loki

Loki renders Storybook stories in a headless Chrome Docker container and compares pixel-perfect screenshots against approved references. Two viewports are tested: **desktop** (1366×768) and **mobile** (iPhone 7).

```bash
npm run test:ui        # compare against references
npm run test:ui:ok     # approve new screenshots as references
npm run test:ui:ci     # strict mode (fails on any diff)
```

---

## CI/CD

GitHub Actions pipeline (`.github/workflows/main.yml`) runs on every push and pull request to `master`:

1. `npm ci` — clean install
2. `npm run build:prod` — production build
3. `npm run lint:ts` — TypeScript lint
4. `npm run lint:scss` — SCSS lint
5. `npm run test:unit` — unit tests

All steps use `if: always()` so failures at one step do not hide failures at subsequent steps. The pipeline targets **Node.js 21.x**.

Pre-commit hooks (Husky + lint-staged) enforce the same lint and format rules locally before every commit.

---

## Project Structure

```
Article-platform-fe/
├── .github/
│   └── workflows/        # CI/CD pipeline definition
├── config/
│   ├── babel/            # Babel presets and plugins
│   ├── build/            # Webpack loaders, plugins, resolver aliases
│   ├── jest/             # Jest configuration and setup files
│   └── storybook/        # Storybook main.ts and preview.ts
├── cypress/
│   ├── e2e/              # End-to-end test specs
│   ├── component/        # Component-level Cypress tests
│   ├── support/          # Custom commands and helpers
│   └── fixtures/         # Static test data
├── docs/                 # Additional documentation (tests.md, storybook.md…)
├── json-server/
│   ├── db.json           # Mock database (users, articles, comments, ratings)
│   └── index.js          # json-server setup with custom route handlers
├── public/
│   └── locales/
│       ├── en/           # English translation files
│       └── ru/           # Russian translation files
├── scripts/              # Code generation and automation scripts
├── src/
│   ├── app/
│   │   ├── providers/    # Redux Store, Theme, Error Boundary, Router providers
│   │   ├── styles/       # Global CSS reset and variables
│   │   └── App.tsx       # Root component
│   ├── pages/            # MainPage, ArticlesPage, ArticleDetailsPage,
│   │                     # ArticleEditPage, ProfilePage, AdminPanelPage…
│   ├── widgets/          # Navbar, Sidebar, ArticlesFilters,
│   │                     # ScrollToolbar, ArticleAdditionalInfo…
│   ├── features/         # AuthByUsername, articleRating, addCommentForm,
│   │                     # editableProfileCard, ThemeSwitcher, LangSwitcher…
│   ├── entities/         # user, article, profile, comment,
│   │                     # notification, rating, country, currency
│   └── shared/
│       ├── ui/
│       │   ├── deprecated/   # Original design system components
│       │   └── redesigned/   # New design system components
│       ├── lib/
│       │   ├── hooks/        # useTheme, useDebounce, useInfiniteScroll…
│       │   ├── components/   # DynamicModuleLoader
│       │   └── features/     # toggleFeatures utility
│       ├── api/              # Axios instance, RTK Query base config
│       ├── config/           # i18n setup, Storybook decorators
│       ├── const/            # Route paths, theme names, localStorage keys
│       └── types/            # Shared TypeScript interfaces
├── webpack.config.ts         # Webpack entry point
├── vite.config.ts            # Vite configuration
├── tsconfig.json             # TypeScript compiler config
└── package.json
```

---

## What This Project Demonstrates

This is not a toy project. The codebase reflects real-world enterprise frontend practices:

| Area | Implementation |
|---|---|
| **Scalable architecture** | Feature Sliced Design with enforced layer isolation via ESLint |
| **TypeScript** | Typed thunks, selectors, API responses, component props throughout |
| **State management** | Redux Toolkit slices, RTK Query endpoints, dynamic reducer injection |
| **Testing culture** | Three test layers: unit, E2E, and visual regression |
| **Code quality** | ESLint (Airbnb + FSD rules), Stylelint, Prettier, Husky pre-commit |
| **Performance** | Code splitting, lazy routes, lazy reducers, debounce, infinite scroll |
| **Internationalization** | Full bilingual support with namespace-separated translation files |
| **Design system migration** | Dual UI kit with runtime feature flag — zero big-bang rewrites |
| **Build tooling** | Hand-crafted Webpack 5 config + Vite as a zero-config alternative |
| **CI/CD** | Automated build, lint, and test pipeline on every commit |
| **Component documentation** | Storybook stories with API mocking and theme/language variants |
