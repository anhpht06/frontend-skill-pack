---
name: Design System, Accessibility & i18n
description: Standards for design systems, a11y, layout grids, and internationalization.
---

# Design System, Accessibility (a11y) & i18n Baseline

This document outlines the UI standards to ensure accessibility, responsiveness, and multi-language support.

## 1. Accessibility (a11y) Baseline
All shared components (e.g., Buttons, Modals, Inputs) **MUST** meet the following standards:

- **Focus Ring:** Always provide a clear focus ring for keyboard users. Never use CSS `outline: none` to hide focus borders.
  - Use Tailwind classes: `focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-foreground`
- **ARIA Attributes:**
  - If a Button only contains an Icon (no text), it MUST have an `aria-label` attribute.
  - For decorative Icons, use `aria-hidden="true"`.
  - Every input field must be linked to a `<label>` via an id, or utilize `aria-label`.
  - Dynamically updated regions (like error messages, loading states) should use `aria-live="polite"` or `aria-busy="true"`.
- **Keyboard Navigation:**
  - All interactive elements must be focusable using the `Tab` key.
  - **Modal/Dialog:** Must implement a "Focus Trap" (confining focus inside the modal when open), and return focus to the trigger button upon closing.
  - **Dropdown/Menu:** Must support the `Escape` key to close.

## 2. Responsive Grid & Layout
- Apply a **Mobile-first** approach: Write base classes for mobile screens first, then use breakpoints (`sm:`, `md:`, `lg:`) to override styles for larger screens.
- **Do not hardcode static pixel `width`s** for major layout components. Rely on Tailwind's Grid/Flexbox system.
- Wrap content inside a standard container: `max-w-6xl mx-auto px-4 sm:px-6 lg:px-8`.

## 3. i18n Framework (Internationalization)
The project must be prepared for multi-language scaling (Especially providing solid initial support for primary target languages).

- **Do not hardcode display strings directly within JSX**.
  ```tsx
  // ❌ Bad
  <button>Login</button>
  
  // ✅ Good
  <button>{t("auth.login")}</button>
  ```
- Organize translation files in the `src/locales/` directory (e.g., `vi.json`, `en.json`).
- Data Formatting:
  - Do not manually format dates, numbers, or currencies.
  - Utilize standard browser APIs: `Intl.DateTimeFormat` and `Intl.NumberFormat` along with the current locale for accurate formatting.
- Fonts: Ensure the chosen typography supports the full Unicode range required for the target languages.
