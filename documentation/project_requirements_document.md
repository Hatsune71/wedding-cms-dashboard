# Project Requirements Document (PRD)

## 1. Project Overview

"Wedding CMS Dashboard" is a full-stack web application starter template built on the *Codeguide Starter Fullstack* foundation. Its primary goal is to speed up the creation of a wedding template content management system (CMS) by providing pre-configured authentication, theming, database integration, and a protected dashboard. Instead of reinventing the wheel, developers can immediately focus on wedding-specific features: multi-role access (admin vs. user), CRUD operations for wedding templates, and a polished, responsive UI.

The core problem it solves is the overhead of setting up common infrastructure—auth, routing, ORM, UI components, and Docker—for any modern web project. By delivering a battle-tested scaffold, it reduces time to market, ensures best practices (TypeScript type safety, secure session handling, responsive design), and sets clear success criteria: working role-based auth; an admin panel to manage templates and users; a user interface for couples to edit their personal wedding page; consistent theming; and reliable data persistence in PostgreSQL.

## 2. In-Scope vs. Out-of-Scope

**In-Scope (Version 1.0)**

*   Role-Based Authentication & Authorization (ADMIN / USER) via Better Auth
*   Protected Dashboard (`/dashboard`) with sidebar navigation
*   CRUD operations for wedding templates (create, read, update, delete)
*   Multi-role UI: Admin sees all weddings & user list; User sees only their wedding editor
*   Database schema definitions: `users` with `role`, `weddings` table
*   Next.js App Router pages and API routes for weddings and auth
*   UI components built with shadcn/ui and styled via Tailwind CSS v4
*   Theming system with light/dark mode support (CSS variables)
*   Docker setup for local PostgreSQL
*   Input validation & error handling using Zod
*   Basic unit tests for auth and wedding APIs

**Out-of-Scope (Planned for Later Phases)**

*   Rich-text or block-based content editor (e.g., TipTap integration)
*   File upload (images/documents) and media management
*   Payment gateway integration or subscription flows
*   Email notifications (invitations, reminders)
*   Analytics dashboard or reporting beyond basic metrics
*   Multi-tenant support or white-label branding
*   Mobile-specific native app or React Native support

## 3. User Flow

A new visitor arrives at the landing page and clicks **Sign Up**. They fill out email/password fields and submit; the system creates their account with a default `USER` role. After sign-up or sign-in, they are redirected to `/dashboard`. Here, the left sidebar shows personalized navigation: "My Wedding" and "Account Settings." The main area displays a form and data table for editing their wedding details—name, date, venue, and other fields. Changes are validated (via Zod), sent to `/api/weddings`, and saved in PostgreSQL, with success or error feedback.

An administrator logs in similarly but holds the `ADMIN` role. On `/dashboard`, the sidebar includes additional links: "Manage Weddings" and "User Management." Clicking "Manage Weddings" opens a data table listing all wedding templates with actions: Create, Edit, Delete. The admin can open a shadcn/ui modal form to add or update templates. Under "User Management," the admin views every user, their roles, and can update roles via a protected API endpoint. All sensitive routes are guarded by middleware that checks session validity and user role before rendering pages or responding to API calls.

## 4. Core Features

*   **Authentication & Authorization**: Sign-up/sign-in, session management, RBAC (role-based access control) with `ADMIN` & `USER` roles.
*   **Multi-Role Dashboard**: Shared layout with conditional nav items and content based on user role.
*   **Wedding Templates CRUD**: Next.js API routes (`/api/weddings`) for create/read/update/delete operations managed through Drizzle ORM.
*   **Database Schema**: `users` table extended with `role` enum; new `weddings` table schema in `db/schema/weddings.ts`.
*   **UI Components**: Reusable elements (`data-table.tsx`, `app-sidebar.tsx`, forms via shadcn/ui).
*   **Theming & Styling**: Light/dark mode toggle, CSS-variable theming, Tailwind CSS utilities.
*   **Form Validation**: Zod schemas for robust input validation on both client and server.
*   **Docker Development Environment**: `docker-compose` for spinning up a local PostgreSQL instance.
*   **Error Handling & Feedback**: Standardized JSON responses with clear success/error messages.
*   **Basic Testing**: Unit tests for auth flows and wedding API endpoints.

## 5. Tech Stack & Tools

**Frontend**

*   Next.js (App Router) – React framework for SSR & file-based routing
*   TypeScript – type safety on both client and server
*   shadcn/ui – accessible, customizable React components
*   Tailwind CSS v4 – utility-first styling

**Backend**

*   Next.js API Routes – Node.js serverless functions for CRUD endpoints
*   PostgreSQL – relational database for storing users and weddings
*   Drizzle ORM – type-safe database queries & migrations
*   Better Auth – authentication library for sign-up/sign-in flows
*   Zod – schema validation for request payloads

**Dev & Ops**

*   Docker & Docker Compose – containerize local Postgres
*   ESLint & Prettier – code linting and formatting
*   GitHub Actions (optional) – CI for tests and linting

## 6. Non-Functional Requirements

*   **Performance**: Initial page load < 2s on 3G; API responds < 200 ms under normal load.
*   **Security**: HTTPS enforced, secure cookies, CSRF/XSS protection, hashed passwords, role checks on all protected routes.
*   **Reliability**: 99.9% uptime; retry logic on transient DB errors.
*   **Scalability**: Able to handle hundreds of concurrent users; stateless API routes.
*   **Usability & Accessibility**: WCAG AA compliance for core pages; responsive design for desktop and tablet.
*   **Maintainability**: 80%+ unit test coverage; TypeScript strict mode; clear code comments.

## 7. Constraints & Assumptions

*   **Environment**: Node.js 18+, Docker installed, environment variables (`DATABASE_URL`, `AUTH_SECRET`) provided.
*   **Dependencies**: Better Auth service availability; PostgreSQL version >= 14.
*   **Assumptions**: Single-tenant application; no legacy data migration; email deliverability configured externally.
*   **Third-Party Limits**: No hard rate limits, but avoid spamming auth endpoints.

## 8. Known Issues & Potential Pitfalls

*   **Role Enforcement Gaps**: Forgetting to apply middleware on new API routes. Mitigation: centralize role check logic in `middleware.ts`.
*   **Schema Drift**: Drizzle migrations out of sync with code. Mitigation: adopt a strict migration workflow and run `drizzle-kit` on CI.
*   **Validation Inconsistencies**: Duplicate validation logic on client/server. Mitigation: share Zod schemas across both.
*   **Docker Networking**: Postgres container not reachable due to port conflicts. Mitigation: document default ports and allow overrides via `.env`.
*   **Performance in Large Datasets**: Data table listing thousands of weddings. Mitigation: implement server-side pagination and indexing on key columns.

This PRD provides a clear, unambiguous blueprint for building the Wedding CMS Dashboard. Every element—from user flows and core features to tech choices and known pitfalls—is spelled out to guide subsequent technical documents without guesswork.
