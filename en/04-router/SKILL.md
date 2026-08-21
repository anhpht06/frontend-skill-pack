---
name: Routing & Navigation (Next.js)
description: Guidelines for routing, navigation, and file structure in Next.js App Router.
---

# Routing & Navigation with Next.js App Router

This document defines the rules for working with the Next.js routing system.

## 1. Route Name Management
- All routing strings (URL paths) **MUST** be centrally declared in the `src/constants/routes.ts` file.
- **Never hardcode URL strings directly within Components.**
  ```typescript
  // src/constants/routes.ts
  export const ROUTES = {
    HOME: "/",
    LOGIN: "/login",
    PROFILE: (id: string) => `/profile/${id}`,
  };

  // ❌ Bad
  <Link href="/login">Login</Link>
  
  // ✅ Good
  <Link href={ROUTES.LOGIN}>Login</Link>
  ```

## 2. App Router Directory Structure (`app/`)
Strictly follow Next.js special file conventions:
- `page.tsx`: The main UI for a route (Server Component by default).
- `layout.tsx`: Wraps child pages (Shared Navbar, Sidebar).
- `loading.tsx`: Displays Skeleton UI while fetching data.
- `error.tsx`: Must integrate `AppError` with a retry button. Note that this file requires `"use client"`.
- `not-found.tsx`: Displays for invalid URLs or when calling `notFound()`.

## 3. Protected Routes
- Authentication checks (Is the user logged in?) to block access to internal pages **must be handled at the Server level via `middleware.ts`**.
- Do not perform Auth checks and Redirects scattered across individual Client Components (causes layout shifts/flickering).

## 4. Navigation
- Use `<Link>`: For standard UI navigation (good for SEO and prefetching).
- Use `router.push()` (from `next/navigation`'s `useRouter`): Use **after completing an action** (e.g., Form submit button, after successful login).
- Use `redirect()` (from `next/navigation`): Use when direct redirection is needed from a **Server Component** or Route Handler/Server Action.

## 5. Loading States
- Prioritize using `loading.tsx` with a **Skeleton UI** that matches the page layout.
- Avoid using full-page spinners which create a poor user experience (blocking UI).
