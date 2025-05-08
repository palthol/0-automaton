# Software Design Document: Automaton Project

**Version:** 1.0
**Date:** May 8, 2025

## 1. Introduction

### 1.1 Project Purpose

The Automaton project aims to establish a robust and scalable frontend infrastructure for building multiple, distinct websites that share a common set of foundational UI components and design principles. The core objectives are:

- **Reusability:** Develop a library of universal UI components (e.g., headers, footers, buttons, cards) that can be seamlessly integrated across various website projects.
- **Themability & Customization:** Implement a flexible theming system allowing each website to have a unique visual appearance (colors, gradients, typography, potentially 3D elements) by configuring shared components.
- **Streamlined Development:** Accelerate the creation of new websites by leveraging pre-built components and a standardized architecture.
- **Maintainability:** Centralize common UI logic and styling to simplify updates and ensure consistency.
- **Scalability:** Design the system to accommodate a growing number of websites and an expanding library of shared components.

The foundational technologies for this project are React, React Router 7, and Tailwind CSS 4.

### 1.2 Scope

This document primarily covers the architectural design of the shared UI library, the theming mechanism, and the structure for individual website applications within the Automaton monorepo. It will detail the purpose of key directories and initial foundational components.

### 1.3 Definitions, Acronyms, and Abbreviations

- **SDD:** Software Design Document
- **UI:** User Interface
- **CSS:** Cascading Style Sheets
- **Monorepo:** A single repository containing multiple distinct projects with well-defined relationships.

## 2. System Architecture

### 2.1 Architectural Overview

The Automaton project will be structured as a **monorepo**. This approach facilitates the management of shared packages (like the UI component library) alongside the individual website applications that consume them.

The core architectural components are:

1.  **Shared UI Library (`packages/ui-core`):** Contains universal React components styled with Tailwind CSS. These components are designed to be themeable.
2.  **Theme Definitions (`packages/theme-definitions`):** (Optional, can be part of `ui-core` or individual apps initially) Provides structures, utilities, or predefined theme configurations that websites can adopt or extend.
3.  **Website Applications (`apps/*`):** Individual website projects (e.g., Next.js, Vite applications) that consume the `ui-core` library and apply their specific branding, content, and routing.

### 2.2 Directory Structure and Purpose

```
/0-automaton/
├── apps/                     # Contains individual website applications
│   └── (e.g., website-one/, website-two/)
├── packages/                 # Contains shared libraries/packages
│   ├── theme-definitions/    # Utilities, interfaces, or base structures for themes
│   └── ui-core/              # The shared universal UI component library
└── SOFTWARE_DESIGN_DOCUMENT.md # This document
└── (other monorepo config files like pnpm-workspace.yaml, package.json)
```

- **`/` (Root Directory - `c:\1-gen\Coding\4-Web-Based-projects\0-automaton`)**

  - **Purpose:** The main entry point for the monorepo. Contains global configuration files for the workspace manager (e.g., pnpm, yarn, npm workspaces, Turborepo, Nx).
  - **Key Files (Conceptual/Typical):**
    - `package.json`: Root package file for managing workspaces and global dev dependencies.
    - `pnpm-workspace.yaml` (if using pnpm workspaces): Defines workspace locations.
    - `tsconfig.base.json` (optional): Base TypeScript configuration for the monorepo.

- **`/apps/`**

  - **Purpose:** Houses individual, deployable website applications. Each subdirectory within `apps/` represents a distinct website.
  - **Example Subdirectories:**
    - `apps/website-one/`: Contains all source code, configuration, and assets for "Website One".
    - `apps/website-two/`: Contains all source code, configuration, and assets for "Website Two".
  - **Key Files (within each app, e.g., `apps/website-one/`):**
    - `package.json`: Manages dependencies specific to this website.
    - `vite.config.ts` or `next.config.js`: Build tool configuration.
    - `tailwind.config.js`: Tailwind CSS configuration, extending the base from `ui-core` and applying app-specific content paths.
    - `src/` or `app/`: Main source code directory for the website (pages, app-specific components, layouts, styles).
    - `src/styles/theme.css` (Conceptual): CSS file defining the site-specific theme using CSS custom properties.

- **`/packages/`**

  - **Purpose:** Contains shared libraries or packages that can be used by multiple applications within the `apps/` directory or by other packages.

- **`/packages/theme-definitions/`**

  - **Purpose:** To provide a centralized place for defining theme structures, TypeScript interfaces for theme objects, utility functions for theme manipulation, or even pre-defined theme palettes that websites can import and use. This helps maintain consistency in how themes are structured across different sites.
  - **Key Files (Conceptual):**
    - `index.ts`: Exports theme-related types, interfaces, and utilities.
    - `palettes.ts`: Example pre-defined color palettes.
    - `types.ts`: TypeScript definitions for theme objects.

- **`/packages/ui-core/`**
  - **Purpose:** The heart of the reusable system. This package contains the universal React components (e.g., `Header`, `Footer`, `Button`, `Card`) styled with Tailwind CSS. These components are designed to be agnostic of specific website content and highly themeable through CSS custom properties and props.
  - **Key Files/Directories (Conceptual):**
    - `package.json`: Defines dependencies for the UI library itself.
    - `tailwind.config.js`: Base Tailwind CSS configuration for the UI components. It will define semantic CSS variables for colors, fonts, etc., that consuming applications will override.
    - `src/`: Source code for the UI components.
      - `src/components/`: Directory for individual React components (e.g., `Header.tsx`, `Footer.tsx`).
      - `src/hooks/`: Custom React hooks shared across UI components.
      - `src/styles/`: Base styles or utilities for `ui-core` if needed.
      - `src/index.ts`: Main export file for the `ui-core` library, making components available for import.

## 3. Core Components & Concepts (Initial Focus)

### 3.1 Universal UI Components (`ui-core`)

These components form the building blocks for all websites.

- **`Header.tsx`:** A themeable header component.
  - **Props:** `logo`, `navItems` (array), `actions` (ReactNode), `className`.
  - **Styling:** Uses Tailwind classes referencing CSS variables (e.g., `bg-surface`, `text-on-surface`).
- **`MainSection.tsx`:** A flexible main content area.
  - **Props:** `children`, `className`.
  - **Styling:** Uses Tailwind classes referencing CSS variables (e.g., `bg-background`, `text-on-background`).
- **`Footer.tsx`:** A themeable footer component.
  - **Props:** `copyrightText`, `className`.
  - **Styling:** Uses Tailwind classes referencing CSS variables.

### 3.2 Theming System

The "handler" for changing visual appearance will primarily rely on:

1.  **CSS Custom Properties:** Each website application (`apps/*`) will define a set of CSS custom properties (variables) in a global CSS file (e.g., `theme.css`). These variables will represent the specific color palette, font choices, gradient definitions, etc., for that site.
    - Example: `:root { --color-primary: <value>; --color-background: <value>; }`
2.  **Tailwind CSS Integration:**
    - The `ui-core` Tailwind configuration (`packages/ui-core/tailwind.config.js`) will define its `theme.extend.colors` (and other properties like `fontFamily`, `spacing`) using these CSS variables.
      - Example: `primary: 'rgb(var(--color-primary) / <alpha-value>)'`
    - Each website's Tailwind configuration (`apps/website-one/tailwind.config.js`) will use the `ui-core` config as a preset and specify its own `content` paths.
3.  **Theme Configuration Files (Optional):** For more complex theming aspects (e.g., parameters for a 3D canvas, specific animation timings), a JavaScript/JSON configuration file within each app can store these values. These can be consumed by a React Context or directly by components.

### 3.3 Website Applications (`apps`)

Each subdirectory in `/apps` will be a standalone React application (e.g., created with Vite or Next.js).

- It will install `@my-org/ui-core` (or the relevant package name) as a dependency (linked via the monorepo).
- It will define its own routes using React Router 7.
- It will implement its specific `theme.css` to style the universal components.
- It will contain pages and app-specific components that compose the universal components from `ui-core`.

## 4. Next Steps

1.  Initialize the monorepo structure using a workspace manager (e.g., pnpm).
2.  Set up the `ui-core` package with React, TypeScript, and Tailwind CSS.
3.  Implement the base `tailwind.config.js` in `ui-core` using CSS variable placeholders.
4.  Develop the initial `Header`, `MainSection`, and `Footer` components within `ui-core`.
5.  Create a sample website application in `apps/` (e.g., `apps/website-one`).
6.  Configure the sample website to consume `ui-core` and define its own `theme.css`.
7.  Implement a basic layout in the sample website using the shared components.
8.  Set up Storybook for `ui-core` for isolated component development and theming previews.

This document will evolve as the project progresses.
