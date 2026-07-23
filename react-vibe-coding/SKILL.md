---
name: react-vibe-coding
description: Guides AI agents in high-velocity React application and component development using JS (ES2020+) or TypeScript, Clean Architecture (Domain, Use Cases, UI, Adapters), responsive layout protocols (Mobile-first vs Desktop fallback), strict project linting/formatting, JSDoc annotations, and performance optimization rules from AGENTS.md.
---

# React Vibe Coding Skill

This skill empowers AI agents to generate high-quality, maintainable, and architecturally sound React web application code at maximum velocity ("vibe coding") without sacrificing structural integrity, responsiveness, or performance standards.

---

## 0. Project Compatibility Check & Force Override (STRICT)

Before processing any React code task, execute the following evaluation:

### 1. Check for Force Override

- Scan the user prompt for explicit force keywords (e.g., `force`, `override`, `override junie decision`, `run anyway`).
- **If detected:** Bypass compatibility checks and proceed with skill execution.

### 2. Validate Environment Compatibility

- Inspect the project root configuration files (primarily `package.json`).
- **Target Language:** Must be a JavaScript or TypeScript project environment.
- **Target Library:** Must contain React (e.g., `react` or `react-dom` declared in `dependencies` or `devDependencies`).

### 3. Automatic Decline Rules

- **Non-JS/TS Projects:** Decline execution immediately if the codebase uses non-JS/TS languages (e.g., Java, Python, Go, Rust).
- **Non-React Projects:** Decline execution immediately if the codebase uses other frameworks (e.g., Angular, Vue, Svelte).

### 4. Decline Response Standard

When declining, output this standard notification:

> 🛑 **Skill Execution Stopped (`react-vibe-coding`)**
> This skill is strictly intended for React (JS/TS) codebases.
>
> **Reason:** The project root does not contain `react` or `react-dom` in `package.json`. If you wish to force execution anyway, include `force` or `override` in your request.

---

## 1. Responsive Layout Protocol (STRICT)

When creating or modifying web UI components, apply the following responsive requirements:

### Clarification Check

If responsiveness is not explicitly mentioned in the user prompt:

- **Option A:** Ask the user: _"Should this component support a responsive layout?"_
- **Option B (Default):** Proceed assuming **Responsive = YES**.

### Standard 1: Responsive = YES (Default)

- Apply Mobile-First CSS / Tailwind practices covering Mobile, Tablet, and Desktop viewports.
- Ensure fluid layout adaptation across breakpoints (`sm:`, `md:`, `lg:`, `xl:`).

### Standard 2: Responsive = NO (Desktop Only)

- Render full-featured UI for Desktop viewports.
- For Mobile and Tablet viewports, render a fallback container displaying static text:
  > `"Open desktop browser to use this feature"`
- This preserves mobile state handling while preventing broken responsive UI.

---

## 2. Language & Version Rules (STRICT)

- **JavaScript:** Enforce **ES2020+** standard (Optional Chaining `?.`, Nullish Coalescing `??`, `async/await`, destructuring, arrow functions).
- **TypeScript:** Inspect `package.json` and `tsconfig.json` to identify the exact TypeScript version and conform strictly to project types, interfaces, and strict null checks.

---

## 3. Dependency Management (STRICT)

- **NEVER** automatically execute shell commands (`npm install`, `yarn add`, `pnpm add`, `bun add`) to install dependencies.
- Suggest required packages at the end of the response using standard Markdown:

````markdown
### 📦 Required Dependencies

```bash
npm install <package-name-1> <package-name-2>
```
````

---

## 4. Clean Architecture (Uncle Bob Principles)

Structure React code using distinct separation of concerns:

| Layer                             | Recommended Paths              | Responsibility & Constraints                                                                |
| :-------------------------------- | :----------------------------- | :------------------------------------------------------------------------------------------ |
| **Domain / Entities**             | `/domain`, `/types`, `/models` | Pure business rules, data models, and type definitions. **Zero React dependencies.**        |
| **Use Cases / Application Layer** | `/hooks`, `/use-cases`         | Custom React hooks orchestrating state management, side effects, and business workflows.    |
| **Presentation / UI Layer**       | `/components`, `/views`        | Declarative UI components focused strictly on rendering and prop handling. Thin components. |
| **Adapters / Infrastructure**     | `/services`, `/api`, `/lib`    | HTTP client wrappers, REST/GraphQL endpoints, SDK connectors, local storage wrappers.       |

---

## 5. Concise Code Documentation (JSDoc Standard)

Every export (component, custom hook, service function, domain interface) **MUST** include a concise 1–3 line JSDoc comment explaining its purpose and responsibility.

### Examples

#### A. Presentational Component

```typescript
/**
 * Renders user profile details in a card layout.
 * Supports desktop view and static fallback on mobile when non-responsive.
 */
export const UserProfileCard: React.FC<UserProfileCardProps> = ({ user }) => {
  // ...
};
```

#### B. Custom Hook (Use Case)

```typescript
/**
 * Manages user authentication state, token refresh cycles, and session expiry.
 */
export const useAuthSession = () => {
  // ...
};
```

#### C. Service / Adapter Function

```typescript
/**
 * Sends a POST request to update user preferences via the REST API gateway.
 */
export const updateUserPreferences = async (
  userId: string,
  prefs: UserPrefs,
): Promise<void> => {
  // ...
};
```

#### D. Domain Interface / Entity

```typescript
/**
 * Represents the core User entity within the domain layer.
 */
export interface UserEntity {
  id: string;
  email: string;
  role: UserRole;
}
```

---

## 6. Code Style & Performance Guidelines

- **Linting & Formatting:** Scan and conform strictly to local ESLint and Prettier configurations (`.eslintrc`, `eslint.config.js`, `.prettierrc`).
- **Performance Best Practices:** Inspect `./AGENTS.md` (or `rules/` directory) for advanced React performance guidelines:
  - Eliminate async waterfalls (`Promise.all`, deferred `await`).
  - Avoid barrel file imports (`import { x } from '.'`).
  - Compute derived state during render (avoid unnecessary `useEffect` state syncing).
  - Optimize re-renders with stable references (`useCallback`, `useMemo`).

---

## 7. Operational Workflow for AI Agents

When executing a task under this skill, follow this step-by-step checklist:

1. **Compatibility Gate:** Check `package.json` for React. Handle force overrides or issue standard decline notification.
2. **Layout Protocol Check:** Determine responsive layout mode (Mobile-First default vs Desktop-Only with fallback text `"Open desktop browser to use this feature"`).
3. **Clean Architecture Decomposition:**
   - Model data structures in `/domain` or `/types`.
   - Extract stateful logic & side effects into custom hooks (`/hooks`).
   - Keep UI components thin and declarative (`/components`).
   - Wrap external calls in `/services`.
4. **Code Generation:** Write code incorporating ES2020+/TS rules, 1–3 line JSDoc comments per export, and local ESLint formatting rules.
5. **Dependency Notice:** If new packages are needed, append the non-executing `npm install` markdown block.
