# Review instructions

Rules for automated code reviews on this repository. General project
conventions live in [CONTRIBUTING.md](CONTRIBUTING.md).

## What Important means here

Reserve Important for findings that would break the app for users, leak
data, or block a rollback:

- Incorrect logic, a render crash, an unhandled rejected promise, or an
  error state that leaves the screen blank
- The backend key, or any server-only environment variable, reachable from
  browser code: a `NEXT_PUBLIC_` variable, an import of `src/lib/backend.ts`
  from a Client Component, or a browser call to the backend instead of the
  `/api/*` proxy routes
- Dates computed with the device's timezone (`new Date().getHours()`,
  `toLocaleString()` without `timeZone`) instead of the `Europe/Paris`
  helpers in `src/lib/dateUtils.ts`
- A server/client mismatch: browser-only APIs (`window`, `localStorage`)
  used during render in a Server Component, or in a way that causes a
  hydration error
- A change in how an API response is read that no longer matches
  `src/types/api.ts` or the backend contract
- Polling hooks that leak: an interval, a timeout or a fetch not cleaned up
  when the component unmounts

Style, naming and refactoring suggestions are Nit at most.

## Cap the nits

Report at most five Nits per review. If you found more, say "plus N
similar items" in the summary instead of posting them inline. If
everything you found is a Nit, lead the summary with "No blocking issues."

## Do not report

- Anything CI already enforces: ESLint and Prettier, TypeScript errors,
  failing Vitest or Playwright tests, build errors
- `package-lock.json`, `LICENSE`, and version bumps opened by Dependabot
- Wording and formatting in Markdown files, unless a statement is wrong

## Always check

- No `any` and no `@ts-ignore`; API types come from `src/types`
- A new or changed feature has Vitest tests next to the code, and the
  shared fixtures in `src/__fixtures__` are updated when an API shape
  changes (E2E tests use them through the mock backend)
- Layouts work on a small phone screen (mobile first, mostly iPhones):
  no fixed widths that overflow, touch targets large enough
- Keyboard and screen-reader changes stay behind the opt-in accessibility
  mode and don't change the touch experience or the design
- `"use client"` is only added where interactivity requires it
