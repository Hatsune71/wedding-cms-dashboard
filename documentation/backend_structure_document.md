# Backend Structure Document

This document outlines the backend setup for the "Codeguide Starter Fullstack" wedding CMS dashboard project. It covers architecture, database, APIs, hosting, infrastructure, security, monitoring, and maintenance in clear, everyday language.

## 1. Backend Architecture

Overall, the backend is built on top of Next.js API Routes with a layered approach resembling a simple Model-View-Controller (MVC) pattern:

- **Controllers** (Next.js API routes) handle incoming HTTP requests, apply authentication and authorization checks, then invoke business logic.
- **Models** (Drizzle ORM) manage database interactions with PostgreSQL, ensuring type safety and clear queries.
- **Services/Helpers** (custom utility files like `lib/auth.ts`) encapsulate shared logic (e.g., session handling, role checks).

Key design patterns and frameworks:

- Next.js App Router for server-side logic, routing, and middleware support.
- Drizzle ORM provides a fluent, type-safe API to work with SQL.
- Better Auth library manages sign-up, sign-in, and session handling.
- Docker for local database setup and environment consistency.

How this supports project goals:

- **Scalability**: Next.js can run API routes as serverless functions (on Vercel or similar), automatically scaling to handle more users. PostgreSQL on a managed cloud service (e.g., AWS RDS) can scale vertically or horizontally.
- **Maintainability**: Clear separation between routes, data models, and utilities makes the code easy to navigate and extend. TypeScript ensures interface consistency across layers.
- **Performance**: Server-side rendering and caching strategies (inherent to Next.js) reduce client load. Drizzle ORM’s lightweight queries minimize database overhead.

## 2. Database Management

The project uses PostgreSQL (an SQL database) for reliable, relational data storage:

- **Type**: SQL (PostgreSQL).
- **ORM**: Drizzle ORM ensures type-safe queries and migrations.
- **Local Development**: A Docker container runs PostgreSQL, matching the production schema.
- **Connection Pooling**: Managed by Drizzle and environment variables to optimize resource usage.
- **Migrations**: Versioned schema changes stored alongside code, so database evolves with application.

Data organization:

- **Users table**: Stores account information and roles.
- **Wedding Templates table**: Holds template metadata and content.
- (Optionally) **Audit Logs** or **User Sessions** tables can be added for tracking changes and active sessions.

Best practices:

- Secure credentials in environment variables (never check them into source control).
- Automate backups on the managed database service.
- Validate inputs at both the API (using Zod or similar) and database level (constraints, enums).

## 3. Database Schema

Below is the core database schema for PostgreSQL in human-readable form, followed by SQL statements.

### Human-Readable Schema

1. **Users**
   - `id`: Unique identifier (UUID)
   - `email`: User’s email address (unique)
   - `password_hash`: Securely hashed password
   - `role`: Enum with values `ADMIN` or `USER`
   - `created_at` & `updated_at`: Timestamps

2. **Wedding_Templates**
   - `id`: Unique identifier (UUID)
   - `name`: Name of the template
   - `description`: Optional text description
   - `data`: JSON object holding template content and settings
   - `owner_id`: References `Users.id` (who created it)
   - `created_at` & `updated_at`: Timestamps

### PostgreSQL Schema (SQL)

```sql
-- Users table
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email TEXT NOT NULL UNIQUE,
  password_hash TEXT NOT NULL,
  role TEXT NOT NULL CHECK (role IN ('ADMIN', 'USER')),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Wedding Templates table
CREATE TABLE wedding_templates (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  description TEXT,
  data JSONB NOT NULL,
  owner_id UUID REFERENCES users(id) ON DELETE CASCADE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Indexes to speed up common queries
CREATE INDEX idx_templates_owner ON wedding_templates(owner_id);
CREATE INDEX idx_users_email ON users(email);
```  

## 4. API Design and Endpoints

The backend exposes RESTful endpoints under `/api` (handled by Next.js). Each endpoint enforces authentication and, where needed, role-based authorization.

Main endpoints:

- **Authentication**
  - `POST /api/auth/signup` — Create a new user account.
  - `POST /api/auth/signin` — Log in and start a session.
  - `POST /api/auth/signout` — End the current session.

- **Wedding Templates**
  - `GET /api/weddings` — List all templates (Admins only see all; users see their own).
  - `POST /api/weddings` — Create a new template (Authenticated users).
  - `GET /api/weddings/[id]` — Retrieve a specific template (Owner or Admin).
  - `PUT /api/weddings/[id]` — Update a template (Owner or Admin).
  - `DELETE /api/weddings/[id]` — Delete a template (Owner or Admin).

- **User Management** (Admins only)
  - `GET /api/users` — List all users.
  - `PATCH /api/users/[id]/role` — Update a user’s role.

How endpoints communicate:

1. Client makes an HTTP request.
2. Next.js API route checks session via Better Auth.
3. Middleware or route logic verifies the user’s role for restricted actions.
4. Drizzle ORM runs the database query.
5. Route returns a JSON response with data or error messages.

## 5. Hosting Solutions

The recommended hosting setup:

- **Frontend & API**: Vercel (serverless functions) or Netlify. Benefits:
  - Automatic scaling based on traffic.
  - Built-in CDN for static assets.
  - Easy integration with GitHub for continuous deployment.
- **Database**: Managed PostgreSQL on AWS RDS, Google Cloud SQL, or DigitalOcean Managed Databases. Benefits:
  - Automated backups and snapshots.
  - High availability (multi-az replication).
  - Simplified scaling.

This combination is reliable, cost-effective for small to medium projects, and minimizes operational overhead.

## 6. Infrastructure Components

Key infrastructure pieces working together:

- **Load Balancer / Edge Network**: Provided by Vercel or CDN provider, routes traffic to nearest serverless instance.
- **CDN (Content Delivery Network)**: Speeds up delivery of static assets (images, CSS) globally.
- **Docker (Local Dev)**: Runs PostgreSQL locally and ensures everyone on the team has the same environment.
- **Connection Pooling**: Database connections pooled by Drizzle or a dedicated proxy (e.g., PgBouncer) in production.

Optional enhancements:

- **In-Memory Cache**: Redis for caching frequent reads (e.g., template lists).
- **Message Queue**: RabbitMQ or AWS SQS for background tasks (e.g., sending notification emails).

## 7. Security Measures

- **Authentication & Sessions**: Better Auth library issues secure, signed cookies or tokens over HTTPS.
- **Authorization**: Middleware checks user role (`ADMIN` vs. `USER`) before allowing sensitive operations.
- **Encryption**:
  - Data in transit: Enforced HTTPS/TLS for all endpoints.
  - Data at rest: Rely on managed database encryption.
- **Input Validation**: Use libraries like Zod to validate request payloads and prevent injection attacks.
- **Environment Variables**: Store secrets (DB URLs, auth keys) outside of code in `.env` or the hosting provider’s secret manager.
- **Rate Limiting**: Can be added at the edge or application layer to prevent abuse.

## 8. Monitoring and Maintenance

Ongoing health checks and updates keep the backend robust:

- **Logging & Error Tracking**:
  - Integrate Sentry or LogRocket for error reporting and performance tracing.
  - Log key events (e.g., failed logins, CRUD errors) to a centralized service (e.g., Datadog).
- **Metrics & Alerts**:
  - Use Prometheus + Grafana or a hosted service (e.g., New Relic) to watch CPU, memory, request latencies.
  - Set up alerts for high error rates or resource exhaustion.
- **Database Maintenance**:
  - Scheduled backups and restore drills.
  - Routine vacuum/analyze for PostgreSQL.
- **Dependency Updates**:
  - Regularly update NPM packages and Docker images.
  - Automated security scans (e.g., GitHub Dependabot).
- **CI/CD Pipeline**:
  - GitHub Actions or similar to run tests, linting, and automatic deployments on merge to main.

## 9. Conclusion and Overall Backend Summary

This backend structure uses a modern, full-stack JavaScript approach with Next.js API Routes, Drizzle ORM, and PostgreSQL to deliver a scalable, maintainable foundation for your wedding CMS. Key strengths:

- Clear separation of concerns (routes, models, services).
- Role-based access control ensuring security for admin vs. user actions.
- Serverless hosting (Vercel) combined with managed database services for reliability and cost efficiency.
- Infrastructure components (CDN, Docker for local dev, optional caching) working together to optimize performance.

Together, these elements create a robust backend that meets project goals: rapid development of multi-role dashboards, secure data handling, and smooth scaling as user demand grows.