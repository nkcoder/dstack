# Frontend

Read this when the task touches UI code, like components, pages, styles, or client-side state. The project's framework conventions win over this file. For calls between the client and the server, also read `api.md`.

## 1. State, one source for each fact

Most UI bugs come from the same data stored in two places that drift apart.

- Sort state by where it belongs. Server data belongs to the server and is only cached on the client. URL state covers filters, tabs, the current page, and anything worth sharing or bookmarking. Form state is what the user is typing. Local UI state is things like whether a menu is open.
- Server data goes through the project's data-fetching cache, like TanStack Query, SWR, RTK Query, Apollo, or the framework's loaders. Don't copy it into a global store or component state.
- Derive, don't store. If a value can be computed from other state, like a filtered list, a total, or whether a form is valid, compute it during render. Memoize only when measurement shows it's slow.
- Keep state as close as possible to where it's used. Lift it only when two components need it. Global stores are for app-wide state like the current user or the theme.
- Render is a pure function of props and state. Fetching, subscriptions, timers, and analytics live in event handlers, effects, or the data layer, never in render. This is the functional core from the principles, applied to UI.
- Don't use an effect to copy one piece of state into another. Derive it. Clean up every subscription and timer an effect starts.

## 2. Every data view has more than one state

Build all of these, not just the happy path. The missing ones are the most common UI defect.

- **Loading.** Show a skeleton or spinner. Reserve the space so the layout doesn't jump when data arrives.
- **Empty.** Say what's empty and what the user can do next.
- **Error.** Say what went wrong in the user's terms and offer a retry. Never show a blank screen or raw error text.
- **Partial.** Some parts loaded and some failed, or the data is stale. Show what you have and mark what's missing.
- **Success.** The normal view.

Never lose what the user already did, like form input or scroll position, when a request fails.

## 3. Accessibility

This is required, not polish. Aim for WCAG 2.2 AA.

- Use the real element. `<button>` for actions, `<a href>` for navigation, `<label>` for inputs, headings in order, and lists as lists. A `<div onClick>` is not a button.
- Everything works with the keyboard alone, in a sensible tab order, with a visible focus style.
- Every input has a label. Every meaningful image has alt text, and decorative ones get `alt=""`. Every icon-only button has an accessible name.
- Manage focus. Move it into a dialog when it opens, keep it there, and return it to the trigger when the dialog closes. After a route change, move focus to the new content.
- Keep body text contrast at 4.5:1 or higher. Never use color alone to carry meaning.
- Announce async results like errors or "saved" with a live region.
- Respect `prefers-reduced-motion`.
- Use ARIA only when no native element does the job. Wrong ARIA is worse than none.

## 4. Forms

- Validate on the client to help the user, and show each error next to its field in plain words. The server still validates everything.
- Disable the submit button while a submit is in flight, and still make the submit safe to repeat (see `api.md`).
- Keep the user's input when a submit fails. Map server field errors back onto the fields.
- Use the right input `type` and `autocomplete` attributes so browsers and password managers can help.

## 5. Performance, measured from the user's side

- Measure Core Web Vitals. The main content should appear within 2.5 seconds (LCP), the page should respond to input within 200ms (INP), and layout shift should stay under 0.1 (CLS). Use Lighthouse, the browser's performance panel, and the framework's profiler.
- Watch bundle size. Split code by route, lazy-load heavy components that aren't visible at first, and check what a new dependency adds before installing it.
- Serve images at the right size and in modern formats. Give them width and height so the layout doesn't shift, and lazy-load the ones off screen.
- Paginate or virtualize long lists.
- Find unnecessary re-renders with the profiler before fixing them. The usual causes are new object or function props on every render, state placed higher than needed, and context holding values that change often.
- Render on the server or at build time when the content doesn't depend on the user, following the framework's conventions.

## 6. Consistency and layout

- Use the project's existing components, design tokens (colors, spacing, type), and patterns. A new one-off style or component needs a reason.
- Build for phone width first, then check medium and wide screens. Nothing should scroll sideways on a phone.
- If the project supports dark mode, get colors from tokens, never hard-coded values.

## 7. Locale and formatting

- Format dates, times, numbers, and currency on the client with `Intl` or the project's i18n library, in the user's locale and time zone.
- Keep user-facing text in the project's translation files if it has them. Never build a sentence by joining translated pieces, since word order differs between languages.
- Leave room for text that runs 30% longer in other languages.

## 8. Security in the browser

- Never insert raw HTML from data (`innerHTML`, `dangerouslySetInnerHTML`, `v-html`). If you must render user-supplied HTML, sanitize it with a vetted library like DOMPurify.
- Don't store auth tokens in `localStorage` or `sessionStorage`, where any script on the page can read them. Prefer `httpOnly`, `Secure`, `SameSite` cookies.
- Hiding a button is not authorization. The server checks every request.
- Everything shipped to the browser is public. Never put secrets in client code or in public env vars like `NEXT_PUBLIC_*` or `VITE_*`.
- Check redirect URLs taken from query parameters against an allowlist, so the app can't be used to send users to a malicious site.

## Verify

- Test components the way users find things, by role, label, and visible text, Testing Library style. Don't query by CSS class or reach into component state. Assert what the user sees.
- Drive the real page in a browser with the **run** skill or Playwright. Walk through every state (loading, empty, error, success). Force the error state by failing the request at the network layer or stopping the backend.
- Use the page with the keyboard only. Reach every control, see where focus is, and open and close any dialogs.
- Run an automated accessibility check like axe or Lighthouse. It catches only part of the problems, so the keyboard pass still matters.
- Look at phone and desktop widths, and dark mode if the project supports it. Take screenshots and actually look at them.
- Check the browser console for new errors or warnings.

## Review

Look hardest at missing loading, empty, and error states, accessibility (real elements, labels, keyboard, focus), the same data stored in two places, raw HTML insertion, and secrets in client code. An accessibility failure that blocks a user is at least high.
