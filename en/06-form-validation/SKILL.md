---
name: 06-form-validation
description: Form management and validation using React Hook Form and Zod.
---

# Form Management & Validation

This document outlines the rules for handling forms and input validation.

## 1. Standard Tooling
- Must use `react-hook-form` for all forms containing 2 or more fields.
- Use `zod` (or `yup`) for declaring Schema Validations.
- Use `@hookform/resolvers/zod` to connect Schemas to the Form.

## 2. Schema Organization (`src/schemas/`)
- All validation schemas must be extracted into the `src/schemas/` directory; **never write schemas directly inside UI components**.
- This enables schema reusability (e.g., sharing between Frontend and Backend) and keeps components clean.
  ```typescript
  // src/schemas/auth.schema.ts
  import { z } from "zod";

  export const loginSchema = z.object({
    email: z.string().min(1, "Email cannot be empty").email("Invalid email format"),
    password: z.string().min(6, "Password must be at least 6 characters"),
  });

  // Automatically infer Type from Schema
  export type LoginFormValues = z.infer<typeof loginSchema>;
  ```

## 3. API Call Rules on Submit
- Inside the `onSubmit` handler, **DO NOT call API functions directly**.
- Always call the `mutate` (or `mutateAsync`) function from the Custom TanStack Query Hook declared in the UI Logic tier.
  ```typescript
  // ❌ Bad: Calling API directly
  const onSubmit = async (data: LoginFormValues) => {
    await userApi.login(data);
  }

  // ✅ Good: Calling via Mutation Hook
  const loginMutation = useLoginMutation();
  const onSubmit = (data: LoginFormValues) => {
    loginMutation.mutate(data);
  }
  ```

## 4. Error Handling
- Extract error strings from `react-hook-form`'s `errors` object (e.g., `errors.email?.message`).
- **Do not hardcode error strings directly in JSX**. Error strings must be defined inside the Zod Schema.

## 5. Using Controllers for Complex Components
- For native HTML inputs (`<input>`, `<select>`), you can use the `register(...)` function.
- For Custom Components (e.g., 3rd party UI libraries, DatePickers, advanced Selects), **MUST use `<Controller />`** or `useController` to ensure data syncs correctly with the form state.
- **Do not use `any`** when typing Form Component props.
