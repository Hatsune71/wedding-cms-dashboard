# Tech Stack Document - Codeguide Starter Fullstack

## Frontend Technologies

We chose a modern, component-driven approach to build a fast and accessible user interface. Here’s what we use:

- **Next.js (App Router)**
  - Enables server-side rendering and seamless page routing.
  - Provides built-in API routes to simplify server logic alongside your frontend.
- **React with TypeScript**
  - Ensures a type-safe codebase, reducing errors when handling complex data like wedding templates.
  - Offers a rich ecosystem of reusable components.
- **shadcn/ui**
  - A library of accessible React components (buttons, forms, tables, etc.).
  - Speeds up UI development so you can focus on business logic, not styling every element from scratch.
- **Tailwind CSS v4**
  - A utility-first CSS framework that lets you style components with simple class names.
  - Promotes rapid iteration and consistent design without writing custom CSS files.
- **Theming & Dark Mode**
  - Built with CSS variables, so switching between light and dark modes is effortless.
  - Allows easy brand customization to match any wedding theme.

These choices combine to deliver a snappy, responsive dashboard that looks great on both desktop and mobile devices.

## Backend Technologies

The backend is designed to handle data storage, authentication, and business logic securely and efficiently:

- **Next.js API Routes**
  - Host server-side code right alongside your frontend pages.
  - Simplifies deployment since the same framework powers both client and server.
- **Node.js / V8 JavaScript Engine**
  - Provides a scalable, non-blocking runtime for handling multiple requests.
- **PostgreSQL**
  - A powerful relational database for storing users, roles, wedding templates, and related data.
  - Handles complex queries and relationships without sacrificing performance.
- **Drizzle ORM**
  - A type-safe ORM for Node.js and TypeScript.
  - Simplifies database interactions, migrations, and helps avoid common SQL mistakes.
- **Better Auth**
  - Manages user sign-up, sign-in, session handling, and password resets.
  - Easily extended with role-based logic to support both “admin” and “user” accounts.
- **Zod (Optional for Validation)**
  - Validates incoming data shapes for your API endpoints.
  - Ensures that only correctly structured data reaches your database.

Together, these technologies form a robust foundation for CRUD operations, role-based access control, and reliable data persistence.

## Infrastructure and Deployment

We picked tools that make development, collaboration, and deployment smooth and repeatable:

- **Version Control: Git & GitHub**
  - Tracks changes, enables pull requests, and simplifies code reviews.
- **CI/CD: GitHub Actions**
  - Automatically runs tests and linting on each commit.
  - Deploys your application to your hosting platform when changes are merged to the main branch.
- **Containerization: Docker**
  - Defines a local development environment with `docker-compose` for PostgreSQL.
  - Ensures every team member and CI runner uses the same setup.
- **Hosting Platform: Vercel (Recommended)**
  - Optimized for Next.js deployments with built-in support for serverless functions (API routes).
  - Automatic SSL, global CDN, and instant rollbacks.
  - Alternatively, you can deploy to other Node.js hosts or container services (AWS, DigitalOcean, etc.).

This setup guarantees consistent environments, fast feedback loops, and reliable deployments.

## Third-Party Integrations

To avoid rebuilding common services, we integrate trusted external tools:

- **Better Auth**
  - Handles authentication flows (sign-up, sign-in, session management).
  - Eases the implementation of role-based access control.
- **drizzle-orm/postgres**
  - Connects Drizzle ORM to PostgreSQL for seamless data management.
- **Zod**
  - Validates request payloads and API responses, ensuring data integrity.
- **shadcn/ui**
  - Provides pre-built, accessible components (modals, tables, forms).

These services let us focus on unique wedding CMS features rather than reinventing authentication or form validation.

## Security and Performance Considerations

We’ve built-in several measures to protect user data and keep the app responsive:

- **Authentication & Authorization**
  - All protected pages and API routes check for a valid session via `Better Auth`.
  - Middleware enforces role checks (e.g., only admins can call certain CRUD endpoints).
- **Data Validation**
  - Incoming API requests are validated with Zod schemas to block malformed or malicious input.
- **Type Safety**
  - TypeScript and Drizzle ORM reduce runtime errors by catching issues at compile time.
- **Server-Side Rendering & Caching**
  - Next.js server-side rendering delivers fully formed HTML to the browser, improving perceived load times and SEO.
  - Static assets and API routes are cached at the edge when possible (via Vercel’s CDN).
- **Environment Variables & Secrets**
  - Credentials (database URL, auth secrets) are stored securely in environment variables, never checked into code.

## Conclusion and Overall Tech Stack Summary

This full-stack template uses industry-standard tools to jumpstart the development of a wedding CMS with multi-role capabilities. By combining:

- A **Next.js + React + TypeScript** frontend with **Tailwind CSS** and **shadcn/ui** for rapid, accessible UI development.
- A **Next.js API**–driven backend leveraging **PostgreSQL**, **Drizzle ORM**, and **Better Auth** for secure data management and authentication.
- **Docker**, **GitHub Actions**, and **Vercel** for consistent local environments, automated testing, and easy deployments.

we deliver a scalable, maintainable foundation. This stack aligns perfectly with the project goals: providing a polished, role-based dashboard and CMS that wedding planners and end users can rely on. Its modular design and comprehensive documentation let you focus on building custom wedding template features instead of reinventing core infrastructure.