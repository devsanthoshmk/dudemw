# Dude Menswear: menswear clothing store and fashion ecommerce frontend

Dude Menswear is an ecommerce frontend and menswear clothing store application built with Next.js 16, React 19, Prisma, PostgreSQL, and Tailwind CSS.

This menswear clothing store application provides a customer-facing fashion ecommerce frontend alongside an administrative dashboard. Shoppers browse apparel catalog items including shirts, t-shirts, denim jeans, and cargo pants, manage shopping carts, save garments to wishlists, and complete orders through Razorpay payment processing. Store administrators monitor stock levels, process customer orders, create discount coupons, manage promotional banners, and print shipping labels.

This repository is a fork of the upstream project at https://github.com/Mergexhq/dudemw.

## Architecture

The codebase uses the Next.js 16 App Router with TypeScript and domain-driven module boundaries.

- Application routing (`src/app/`):
  - Storefront routes (`src/app/(store)/`): Customer pages for catalog browsing, product details, cart, checkout, order tracking, customer accounts, and store policies.
  - Admin dashboard routes (`src/app/admin/`): Administrative pages for orders, products, inventory, categories, collections, banners, coupons, campaigns, and store settings.
  - API routes (`src/app/api/`): HTTP endpoints for Razorpay webhooks, health checks, cron routines, Instagram feed integration, search, tax calculations, and shipping rates.
  - Middleware (`src/middleware.ts`): Clerk authentication verification, protected route enforcement for customer wishlists, and internal path rewriting for the admin subdomain (`admin.dudemw.com`).
- Domain logic (`src/domains/`): Feature modules that isolate business logic for cart, checkout, product, collections, order, campaign, profile, and admin domains.
- Data access (`src/lib/db.ts` and `prisma/schema.prisma`): Prisma ORM with the `@prisma/adapter-pg` driver connecting through a PostgreSQL connection pool.
- Authentication (`src/lib/auth.ts`, `src/lib/admin-auth.ts`): Clerk authentication handles user sessions and role assignments (`super_admin`, `admin`, `manager`, `staff`), while guest sessions use signed HTTP-only cookies.
- Media storage (`src/lib/cloudinary.ts`): Cloudinary handles image uploads and format optimization.
- Cache layer (`src/lib/services/redis.ts`): Upstash Redis caches homepage collections and banner payloads.
- Document and email generation: `@react-pdf/renderer` generates invoices and packing labels, while Resend sends transactional notifications and cart reminders.

## Setup

### Prerequisites
- Node.js 20.0.0 or higher
- npm 9.0.0 or higher
- PostgreSQL database
- Clerk account for authentication
- Razorpay account for payments

### Installation
1. Clone the repository:
   ```bash
   git clone https://github.com/devsanthoshmk/dudemw.git
   cd dudemw
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Create the environment configuration file:
   ```bash
   cp .env.example .env.local
   ```
   Populate `.env.local` with your database URL, Clerk keys, and service credentials.

4. Generate the Prisma client:
   ```bash
   npx prisma generate
   ```

5. Start the local development server:
   ```bash
   npm run dev
   ```
   The application runs on `http://localhost:3000`.

## Usage

### Command reference
- Run the development server with Turbopack:
  ```bash
  npm run dev
  ```
- Build the standalone production bundle:
  ```bash
  npm run build
  ```
- Inspect production bundle sizes with Next.js bundle analyzer:
  ```bash
  npm run build:analyze
  ```
- Start the production server:
  ```bash
  npm run start
  ```
- Run ESLint checks:
  ```bash
  npm run lint
  ```

### Initial administrative setup
1. Open `http://localhost:3000/admin/setup` in your browser.
2. Enter the `ADMIN_SETUP_KEY` defined in your environment variables.
3. Complete initial administrator registration and record the generated recovery key.
4. Sign in to the management console at `http://localhost:3000/admin/login`.

## Configuration

The application reads configuration through system environment variables. Set these values in `.env.local` for local development or in your production host environment:

### Core application
- `NEXT_PUBLIC_APP_URL`: Canonical URL of the web application (for example `http://localhost:3000` or `https://dudemw.com`).
- `NEXT_PUBLIC_SITE_URL`: Base domain URL used for absolute links and canonical metadata.
- `NODE_ENV`: Runtime environment identifier (`development`, `test`, or `production`).

### Database
- `DATABASE_URL`: PostgreSQL connection string consumed by Prisma and the connection pool.

### Authentication
- `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY`: Clerk public API key for client-side authentication.
- `CLERK_SECRET_KEY`: Clerk private key for server-side session verification.
- `ADMIN_SETUP_KEY`: Shared secret required to register the initial super administrator.
- `ADMIN_EMAILS`: Comma-separated list of administrative email addresses for initial authorization gates.

### Payment processing
- `NEXT_PUBLIC_RAZORPAY_KEY_ID`: Razorpay public key ID for checkout initialization.
- `RAZORPAY_KEY_SECRET`: Razorpay private key secret for payment verification.
- `RAZORPAY_WEBHOOK_SECRET`: Secret key for validating incoming Razorpay webhook signatures.

### Media and caching
- `CLOUDINARY_CLOUD_NAME`: Cloudinary account cloud name for asset uploads.
- `CLOUDINARY_API_KEY`: Cloudinary API credential key.
- `CLOUDINARY_API_SECRET`: Cloudinary API credential secret.
- `UPSTASH_REDIS_REST_URL`: Upstash Redis REST endpoint URL.
- `UPSTASH_REDIS_REST_TOKEN`: Upstash Redis access token.

### Communications and scheduled tasks
- `RESEND_API_KEY`: Resend API key for sending transactional emails.
- `RESEND_FROM_EMAIL`: Sender address for outgoing emails (default: `noreply@dudemw.com`).
- `CRON_SECRET`: Bearer token authorizing scheduled cron job executions.
- `ORDER_TOKEN_SECRET`: Symmetric key used to sign guest order validation tokens.
- `NEXT_PUBLIC_WHATSAPP_NUMBER`: Store WhatsApp contact number for customer support.
- `NEXT_PUBLIC_SUPPORT_EMAIL`: Customer support email address.

## Upstream project

This repository is a fork of the upstream project at https://github.com/Mergexhq/dudemw.
