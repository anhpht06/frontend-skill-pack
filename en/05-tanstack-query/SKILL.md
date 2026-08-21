---
name: 05-tanstack-query
description: 2-tier architecture for API Data Fetching with TanStack Query v5.
---

# Data Fetching & Server State with TanStack Query v5

This document outlines how to call APIs and store Server State using TanStack Query. We utilize a **2-Tier Architecture** to completely separate Logic and UI.

## 1. Pure Logic Tier (`src/services/query/`)
This tier ONLY contains configurations. React Hooks are strictly forbidden here (do not import `useQuery` or `useMutation`).

- **`query-keys/`**: Key management.
  - Must use the `@lukemorales/query-key-factory` library to generate keys.
  - Do not write string keys manually scattered across the codebase.
- **`queries/`**: Data reading configuration.
  - Define functions that return `queryOptions`.
  - Fetcher function rule: **Always write Arrow Functions** `queryFn: () => userApi.getMe()` to keep the Context safe, rather than `queryFn: userApi.getMe`.
- **`mutations/`**: Data writing configuration.
  - Define functions that return the configuration object for a mutation (containing `mutationFn`).

## 2. UI Logic Tier (`src/hooks/`)
This tier is exclusively for calling React Hooks and connecting with the UI.

- **`hooks/queries/`**:
  - Create Custom Hooks (e.g., `useUserQuery`) wrapping `useQuery`.
  - This hook imports the configuration object from the Pure Logic tier above.
  - Do not call APIs directly in components. Any component wanting data must call a custom hook.
- **`hooks/mutations/`**:
  - Create Custom Hooks (e.g., `useLoginMutation`) wrapping `useMutation`.
  - This is the place to handle UI side effects such as: Toast notifications (`onSuccess`, `onError`), page navigation (`router.push`), or closing Modals.

## 3. Cache Configuration Rules (`staleTime`)
Configuring `staleTime` (the time until data is considered old) is crucial:
- Rarely changing data (User profile, settings): `5 - 10 minutes`.
- Frequently changing data (Feeds, notifications): `30 seconds - 1 minute`.
- Real-time data (Chat, stocks): `0` (best combined with WebSockets).

## 4. Optimistic Updates
- Use this pattern for actions like "Liking", "Hearting", or "Checking a Todo" so the UI responds instantly without waiting for the API response.
- Handled in the `onMutate` function of the Custom Mutation Hook: instantly update the UI cache, and revert to previous data in `onError` if the API call fails.
