# Frontend Guideline Document

This document describes the frontend architecture, design principles, and technologies used in the **Codeguide Starter Fullstack** template, tailored for a multi-role wedding CMS and dashboard. It explains how everything fits together so that anyone—developer or non-developer—can understand and extend the frontend setup.

## 1. Frontend Architecture

**Framework & Language**
- **Next.js (App Router)**: Provides file-based routing, server-side rendering (SSR), and API routes. It enforces a clear split between server and client code.
- **React with TypeScript**: Ensures type safety and predictable data models for components and API responses.

**UI Library & Styling**
- **shadcn/ui**: A set of accessible, pre-built React components for forms, tables, modals, etc.
- **Tailwind CSS v4**: A utility-first CSS framework that speeds up styling and enforces consistency.

**How It Supports Scalability & Maintainability**
- **Modular Structure**: Pages live in `app/`, reusable pieces in `components/`, API logic in `app/api/`. This keeps features isolated and easy to locate.
- **TypeScript**: Catches errors at compile time, making large codebases more predictable.
- **Server vs. Client**: Next.js 13’s server components handle data fetching close to the server, reducing client bundle size and improving load times.

**Performance**
- Automatic **code splitting**: Each page only loads the JS it needs.
- **Image optimization** and **asset caching** by Next.js.
- **Lazy loading** of heavy components via dynamic imports when needed (e.g., chart libraries or rich-text editors).

## 2. Design Principles

1. **Usability**: Clear navigation, consistent form layouts, and obvious call-to-action buttons (e.g., “Create Template”).
2. **Accessibility (A11y)**: Keyboard-navigable components, proper ARIA labels (shadcn/ui follows WAI-ARIA best practices), and color contrast checks.
3. **Responsiveness**: Mobile-first design; the layout adapts from phones to tablets to desktops using Tailwind’s responsive utilities.
4. **Consistency**: Shared spacing, typography, and color usage across all screens. Reusable components prevent drift in look and feel.
5. **Role-Based Views**: Interface adapts based on whether you’re an **Admin** (full CMS controls) or a **User** (personal wedding editor).

_Application of Principles_:
- **Forms**: Use the same spacing, labels, and error displays across sign-up, template creation, and profile settings.
- **Navigation**: Sidebar and header show or hide links (e.g., “User Management”) based on user role.

## 3. Styling and Theming

**Styling Approach**
- **Tailwind CSS**: Utility classes (e.g., `px-4`, `text-gray-700`) for rapid, consistent styling without writing custom CSS.
- **Atomic CSS Methodology**: By nature, Tailwind is an atomic approach—small, reusable classes that compose UI.

**Theming**
- **CSS Variables**: Define `--color-primary`, `--color-secondary`, etc., for easy theme overrides.
- **Dark & Light Modes**: Tailwind’s `dark:` variants switch color schemes automatically.

**Visual Style**
- **Modern Flat Design** with subtle **glassmorphism** touches on cards and modals (semi-transparent backgrounds with soft shadows).

**Color Palette**
- Primary Blue: `#3B82F6` (Tailwind’s `blue-500`)
- Secondary Pink: `#EC4899` (`pink-500`)
- Neutral Gray: `#6B7280` (`gray-500`)
- Background Light: `#F9FAFB` (`gray-50`)
- Background Dark: `#1F2937` (`gray-800`)
- Accent Gold: `#FBBF24` (`yellow-400`) for highlights

**Typography**
- **Font Family**: Inter (or system-ui fallback)
- **Heading Scale**: `text-4xl` for main titles, down to `text-base` for body copy.
- **Line Height & Spacing**: Comfortable defaults (`leading-relaxed`, `space-y-4`).

## 4. Component Structure

**Folder Layout**
- `app/` – Top-level pages and nested layouts.
  - `dashboard/` – Holds role-protected areas (`admin/`, `weddings/`).
- `components/` – Reusable UI bits:
  - `auth-buttons.tsx` – Sign-in/out controls.
  - `site-header.tsx`, `app-sidebar.tsx` – Layout shells.
  - `data-table.tsx`, `section-cards.tsx`, `chart-area-interactive.tsx` – Core dashboard widgets.

**Reusability & Maintainability**
- Components accept props (data, callbacks), making them flexible.
- Shared “UI primitives” (buttons, form inputs) live in one place—updates propagate app-wide.
- Clear file names and single responsibility simplify on-boarding and code reviews.

## 5. State Management

- **Server Components** handle initial data fetches (lists of weddings, user profiles) without bundling data-fetch logic on the client.
- **Client Components** (marked `'use client'`) manage interactive state: form fields, modal open/close flags, local filters.
- **React Context** (for theme or session state) – a simple context provider exposes current user info and theme preferences.
- For complex data fetching or caching needs, integrating **React Query** or **SWR** is recommended, though optional in the starter.

## 6. Routing and Navigation

- **Next.js App Router**:
  - File-based routing under `app/` with nested `layout.tsx` files for consistent headers/sidebars.
  - Dynamic routes (e.g., `app/dashboard/weddings/[id]/page.tsx`) for detail and edit views.
- **Protected Routes**:
  - A middleware layer (`middleware.ts`) inspects sessions, redirects unauthenticated users to `/sign-in`, and blocks users without the proper role.
- **Navigation Flow**:
  1. Unauthenticated ➔ `/sign-in` or `/sign-up` pages.
  2. Authenticated ➔ `/dashboard` layout. Sidebar links vary for **Admin** vs. **User**.
  3. Admin ➔ `/dashboard/admin/users`, `/dashboard/weddings` list.
  4. User  ➔ `/dashboard/weddings/[theirId]` editor.

## 7. Performance Optimization

- **Automatic Code Splitting**: Next.js sends only the JavaScript that each page needs.
- **Dynamic Imports**: Heavy components (e.g., rich-text editor) load on demand.
- **Tree-shaking & PurgeCSS**: Tailwind removes unused CSS in production.
- **Image Component**: Next.js `<Image>` optimizes and lazy-loads wedding photos.
- **Caching**:
  - HTTP caching headers on API responses.
  - Client-side caching via React Query or SWR to avoid refetching.

## 8. Testing and Quality Assurance

**Unit & Integration Testing**
- **Jest** with **React Testing Library** for component and hook tests.
- Test critical flows: login, role-based redirects, form validation.

**End-to-End (E2E) Testing**
- **Cypress** (or **Playwright**) to simulate user journeys:
  - Sign up / sign in flows.
  - Admin creating/editing/deleting a wedding template.
  - User editing their wedding details.

**Linting & Formatting**
- **ESLint** with TypeScript rules and Next.js plugin.
- **Prettier** for consistent code style.
- **Husky** + **lint-staged** to run checks on commit.

**Validation**
- **Zod** schemas on both API routes and client forms to enforce data shapes and show user-friendly errors.

## 9. Conclusion and Overall Frontend Summary

This guideline outlines a **modular**, **type-safe**, and **performant** frontend built with Next.js, React, Tailwind CSS, and shadcn/ui. By following these principles—**clear architecture**, **component reuse**, **role-based access**, and **robust testing**—the wedding CMS dashboard will be easy to extend, maintain, and scale. Unique aspects like Next.js server components, utility-first styling, and built-in theming ensure developers can focus on features, not boilerplate, while delivering a polished experience for both admins and end users.