---
name: react-vibe-coding
description: Assists in fast React development using JS (ES2020+) or TS (project version), Clean Architecture, project ESLint/Prettier configs, and concise comments. Validates React JS/TS compatibility before execution unless overridden by force keywords.
---

# React Vibe Coding Skill

This skill guides Junie to generate clean, maintainable React code at high velocity without sacrificing architectural integrity.

## 0. Project Compatibility Check & Force Override (STRICT)
Before processing any code task, execute the following evaluation:

1. **Check for Force Override:**
   * Scan the user prompt for explicit force keywords (e.g., `force`, `override`, `override junie decision`, `run anyway`).
   * If a force keyword is detected, **bypass all compatibility checks** and proceed with skill execution.

2. **Validate Environment Compatibility:**
   * Inspect project root configuration (primarily `package.json`).
   * **Target Language:** Must be a JavaScript or TypeScript project environment.
   * **Target Library:** Must contain React (e.g., `react` or `react-dom` declared in `dependencies` or `devDependencies`).

3. **Automatic Decline Rules:**
   * **Non-JS/TS Projects:** If the codebase is written in another language (e.g., Java, Kotlin, Python, Go, Rust, C#), **decline execution immediately**.
   * **Non-React Projects:** If the project is JS/TS but uses a non-React stack or framework (e.g., Angular, Vue, Svelte, plain Node.js), **decline execution immediately**.

4. **Decline Response Standard:**
   When declining, issue a direct notification detailing the reason:
   > 🛑 **Skill Execution Declined (`react-vibe-coding`)**
   > This skill is restricted to React (JS/TS) codebases. The detected project does not meet the requirements (non-JS/TS project or missing React dependency).
   > 
   > *To bypass this restriction, re-run your prompt with a force keyword (e.g., `force` or `override junie decision`).*

---

## 1. Language Rules (STRICT)
Always check project context to determine the active language:

* **JavaScript:** 
  * Strictly enforce **ES2020 standard and above** (Optional Chaining `?.`, Nullish Coalescing `??`, `async`/`await`, modern array methods, destructuring, arrow functions).
* **TypeScript:** 
  * Inspect `package.json` (under `dependencies` or `devDependencies`) and `tsconfig.json` to identify the **exact TypeScript version** configured for the project.
  * Use syntax, typing features, and strictness options fully compatible with that project-specified TypeScript version.

---

## 2. Dependency Management (STRICT)
* **DO NOT** automatically execute shell commands (`npm install`, `yarn add`, `pnpm add`) to install dependencies.
* Suggest required packages at the end of the response using Markdown formatting:

> ### 📦 Required Dependencies
> Run the following command to install the required package(s):
> ```bash
> npm install <package-name>
> ```

---

## 3. Clean Architecture (Uncle Bob Principles)
Structure React code using distinct separation of concerns:

1. **Domain / Entities (`/domain` or `/types`):**
   * Pure business rules, domain objects, or interfaces. Completely detached from React or framework logic.
2. **Use Cases / Application Layer (`/hooks` or `/use-cases`):**
   * Custom React hooks orchestrating business operations, side effects, and application state. UI components consume these hooks.
3. **Presentation / UI Layer (`/components`):**
   * Declarative UI components focused strictly on rendering and handling props (Single Responsibility Principle).
4. **Adapters / Infrastructure (`/services` or `/api`):**
   * HTTP client wrappers, external API SDKs, or browser storage wrappers.

---

## 4. Short Code Comments
* Every component, custom hook, and non-trivial method **must include a concise comment** or standard JSDoc header (1–3 lines max) explaining its purpose.

```tsx
/**
 * Custom hook managing user authentication session and state transitions.
 */
export const useAuthSession = () => { ... }

/**
 * Presentational button component styled for primary user actions.
 */
export const PrimaryButton = ({ label, onClick }: ButtonProps) => { ... }
```
