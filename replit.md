# replit.md

## Overview

This is a "digital gift box" web application — a romantic single-page experience where a user clicks to open an animated 3D gift box, revealing three interactive items: a love letter (with typewriter animation, 3D flip, music toggle, and confetti), a polaroid photo, and a mini note. The project is built as a full-stack TypeScript app with a React frontend and Express backend, though the core experience is entirely frontend-driven. The backend exists primarily as scaffolding with a simple messages table.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Frontend
- **Framework**: React 18 with TypeScript, bundled by Vite
- **Routing**: Wouter (lightweight client-side router) — single main route (`/`) renders `GiftPage`
- **Styling**: Tailwind CSS with CSS variables for a warm vintage paper aesthetic. Custom fonts: Caveat (handwriting), Playfair Display (display), DM Sans (body)
- **UI Components**: shadcn/ui (new-york style) built on Radix UI primitives, located in `client/src/components/ui/`
- **Animations**: Framer Motion for 3D box opening, item floating, card flipping, and orchestrated transitions
- **Special Effects**: canvas-confetti for celebration effect, lucide-react for icons
- **State Management**: React useState for scene transitions (closed → open → letter view), TanStack React Query for server state
- **Path Aliases**: `@/` → `client/src/`, `@shared/` → `shared/`, `@assets/` → `attached_assets/`

### Key Frontend Components
- `GiftBox` — CSS 3D gift box with lid animation (rotateX), ribbon, and bow
- `Letter` — Fullscreen modal with typewriter text reveal, skip animation, 3D flip (front/back), music toggle, confetti button
- `Polaroid` — Vintage-styled photo card with dialog modal for enlarged view; uses `/assets/minha-foto-nova.jpg`
- `MiniNote` — Small clickable note that opens a dialog with a personal message

### Backend
- **Runtime**: Node.js with Express, TypeScript compiled via tsx
- **Server Entry**: `server/index.ts` creates HTTP server, registers routes, serves static files in production or uses Vite dev middleware in development
- **API Structure**: Minimal REST API defined in `shared/routes.ts` using Zod schemas for validation
  - `GET /api/messages` — list messages
  - `POST /api/messages` — create message
- **Storage**: `DatabaseStorage` class in `server/storage.ts` implementing `IStorage` interface

### Database
- **Database**: PostgreSQL (required via `DATABASE_URL` environment variable)
- **ORM**: Drizzle ORM with `drizzle-zod` for schema-to-Zod conversion
- **Schema**: Single `messages` table in `shared/schema.ts` with fields: `id` (serial), `content` (text), `isRead` (boolean), `createdAt` (timestamp)
- **Migrations**: Drizzle Kit with `db:push` command for schema sync; migration output in `./migrations`

### Build Process
- **Development**: `tsx server/index.ts` with Vite dev server middleware (HMR via WebSocket at `/vite-hmr`)
- **Production Build**: Custom `script/build.ts` — Vite builds client to `dist/public/`, esbuild bundles server to `dist/index.cjs` with selective dependency bundling
- **Static Serving**: In production, Express serves `dist/public/` with SPA fallback to `index.html`

### Shared Code
- `shared/schema.ts` — Database schema and Zod types shared between client and server
- `shared/routes.ts` — API route definitions with Zod validation schemas, used by both frontend hooks and backend handlers

## External Dependencies

### Services & Infrastructure
- **PostgreSQL Database**: Required. Connection via `DATABASE_URL` environment variable. Uses `connect-pg-simple` for session store capability and `pg` Pool for connections
- **Google Fonts CDN**: Caveat, Dancing Script, Playfair Display, DM Sans, Architects Daughter, Fira Code, Geist Mono loaded via `<link>` tags
- **Transparent Textures**: Wood pattern texture loaded from `transparenttextures.com` for background
- **Unsplash**: Fallback polaroid image if local photo is missing

### Key NPM Packages
- **Frontend**: react, framer-motion, canvas-confetti, wouter, @tanstack/react-query, lucide-react
- **UI Library**: Full shadcn/ui component set (Radix UI primitives + Tailwind + CVA)
- **Backend**: express, drizzle-orm, drizzle-zod, pg, zod
- **Build Tools**: vite, esbuild (via build script), tsx, tailwindcss, postcss, autoprefixer
- **Replit Plugins**: @replit/vite-plugin-runtime-error-modal, @replit/vite-plugin-cartographer, @replit/vite-plugin-dev-banner (dev only)