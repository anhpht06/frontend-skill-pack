---
name: Service Layer & Authentication
description: 3-tier API architecture, Anti-Corruption Layer, and advanced Auth Token management.
---

# Service Layer, Anti-Corruption Layer & Auth Token Management

This document outlines the architecture for API handling and Auth Token management.

## 1. 3-Tier Service Architecture

### Tier 1 — Axios Instance (`src/services/axios/`)
- All global configurations (`baseURL`, `timeout`, `withCredentials`) belong here.
- **Must use `axiosInstance`** for all requests. Never use `axios.get()` or `fetch()` directly.
- 401 Interceptor: When a token expires, failing requests must be put into a **Failed Queue** waiting for the token refresh, then automatically retried.

### Tier 2 — API Fetcher & Anti-Corruption Layer (`src/services/api/*.api.ts`)
- Each business domain has its own file (e.g., `user.api.ts`, `product.api.ts`).
- **Mapper Function:** All data returned from the Backend must pass through a Mapper function to be cleaned before being handed to the UI or TanStack Query.
- **No `any` type allowed**. Use `unknown` and Safe Casting (`Record<string, unknown>`).
- **Array Protection (Anti-Corruption):** If the BE returns null/string for a field that should be an array, cast it to an empty array to prevent `list.map is not a function` errors.
  ```typescript
  list: Array.isArray(data.list) ? data.list : []
  ```

### Tier 3 — Logger (`src/services/logger/`)
- Handled errors that do not throw exceptions must be logged via `LoggerService.captureError(...)`. Do not call Sentry SDKs directly.

---

## 2. Auth Token Management (Advanced)

Rules for systems utilizing JWT + Refresh Token.

### Token Holder Pattern
- The JWT is held and centrally managed in a class/module (e.g., `TokenManager`). UI components do not manage tokens themselves.
- **Proactive Refresh:** Automatically trigger a background token refresh API call when the token's time-to-live (TTL) is around ~60 seconds.
- Upon receiving a `401 TOKEN_INVALID` (due to unexpected expiration), automatically trigger the callback to get a new token and retry.

### Operation Lease (Protecting Long-Running Operations)
- Long operations like large file uploads or batch jobs must request an `op_id` (Operation Lease).
- If a token refresh process occurs concurrently while a long operation is running, the SDK **MUST NOT cancel** that long operation.
- The long operation will continue using the old token (which is still valid for that specific transaction) until it completes.

### Distinguishing 401 Errors
The system must clearly distinguish between two types of HTTP 401 errors:
- `401 TOKEN_INVALID`: Token expired → Trigger refresh mechanism → Retry.
- `401 APP_REVOKED`: Account locked, password changed, or permissions revoked → **DO NOT refresh**, throw an Exception straight to the UI tier to redirect to the login screen.

### Mandatory Unit Tests
Any Token management module must satisfy the following test cases:
1. Token is about to expire → automatically calls the refresh callback BEFORE sending the next request.
2. Receives `401 TOKEN_INVALID` amidst a long upload with an Operation Lease → does not cancel the upload, performs background token refresh.
3. Receives `401 APP_REVOKED` → does not retry, throws a clear exception.
