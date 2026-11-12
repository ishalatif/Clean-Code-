---
name: clean-code
description: Universal Clean Code engineering standard. Enforces meaningful naming, small single-responsibility functions, self-documenting code, clean error handling, modular architecture, F.I.R.S.T. unit testing, and 65 code smells/heuristics across any programming language and AI coding agent. Activate whenever writing, refactoring, reviewing, or designing code.
---

# Universal Clean Code Skill

This skill enforces strict, language-agnostic software craftsmanship principles, design patterns, refactoring workflows, and 65 code quality heuristics across **all programming languages** (JavaScript/TypeScript, Python, Go, Java, C#, PHP, Rust, Dart/Flutter, C++, Ruby, SQL, etc.).

---

## When to Activate This Skill
- Writing new features, modules, classes, components, functions, or scripts in any language.
- Refactoring messy, duplicated, deeply nested, or inefficient code.
- Reviewing code quality or self-auditing generated code before delivery.
- Designing domain models, APIs, error-handling strategies, third-party boundaries, or unit tests.

---

## Detailed Reference Guides (Progressive Disclosure)
Read these reference files when you need deep-dive rules, Before/After code patterns, or the full catalog of 65 heuristics:
- **[functions-and-names.md](references/functions-and-names.md)**: Deep dive into Meaningful Names, Small Functions, Self-Documenting Code vs. Comments, and Vertical/Horizontal Formatting.
- **[architecture-objects-and-tests.md](references/architecture-objects-and-tests.md)**: Deep dive into Objects vs. Data Structures, Law of Demeter, Error Handling & Null Safety, Clean Boundaries, F.I.R.S.T. Unit Tests, Classes (SRP, Cohesion, OCP, DIP), Systems & Dependency Injection, Simple Design, Concurrency, and Successive Refinement.
- **[smells-and-heuristics.md](references/smells-and-heuristics.md)**: Complete catalog of all **65 Smells and Heuristics** (`C1–C5`, `E1–E2`, `F1–F4`, `G1–G36`, `J1–J3`, `N1–N7`, `T1–T9`) with detection triggers and exact remediation recipes.

---

## Core Clean Code Workflow

Follow this 4-step workflow every time you write or modify code in any language:

### Step 1: Pre-Coding Simplicity & Design Check
Apply the **Four Rules of Simple Design** (in priority order) before writing code:
1. **Runs All Tests**: Design components so they are decoupled and easily verifiable.
2. **Contains No Duplication (`[G5]` DRY)**: Never duplicate data structures, validation logic, or algorithms.
3. **Expresses Intent**: Write code that reads like clear, well-structured prose.
4. **Minimizes Classes and Methods**: Avoid over-engineering, Big Design Up Front (BDUF), and speculative abstractions. Always use the simplest solution that works.

---

### Step 2: Language-Agnostic Implementation Rules

#### 1. Meaningful Names (`[N1–N7]`)
- **Intention-Revealing (`[N1]`)**: Names must clearly state *why* something exists, *what* it does, and *how* it is used (`elapsedTimeInDays`, `activeUsers`, `calculateMonthlyRevenue`).
- **No Disinformation or Noise**: Never use vague placeholders (`data`, `info`, `temp`, `val`, `res`, `item`, `obj`, `manager`, `processor`, `helper`) when a specific domain name exists. Avoid encoding collection types (`accountList`); use plural nouns (`accounts`).
- **Searchable & No Magic Literals (`[N5, G25]`)**:
  - Replace magic numbers and raw string literals with named constants or enums (`SECONDS_PER_DAY`, `OrderStatus.PAID`).
  - Single-letter variables (`i`, `j`) are allowed **only** as loop counters in tiny 3–5 line scopes.
- **No Encodings (`[N6]`)**: No Hungarian notation (`strName`, `arrItems`), no member prefixes (`m_`), and no `I` prefix on interfaces (`PaymentGateway`, not `IPaymentGateway`).
- **Parts of Speech & Consistency**:
  - **Classes / Types / Structs / Components**: Nouns or noun phrases (`InvoiceGenerator`, `UserProfile`).
  - **Functions / Methods**: Verbs or verb phrases (`validateOrder`, `sendNotification`).
  - **Booleans / Predicates**: Affirmative prefixes (`isReady`, `hasAccess`, `canEdit`). Avoid negative names (`isNotDisabled` -> `[G29]`).
  - **One Word per Concept**: Do not mix `get`, `fetch`, and `retrieve` (or `Controller`, `Manager`, `Service`) for the same concept across a codebase.

#### 2. Small, Focused Functions (`[F1–F4, G30, G34]`)
- **Small!**: Keep functions short (ideally **5–15 lines**, rarely >20 lines).
- **Do One Thing (`[G30]`) at One Level of Abstraction (`[G34]`)**:
  - Every statement inside a function must be **one level of abstraction below** the function's name.
  - Never mix high-level business orchestration with low-level string manipulation, array indexing, or byte formatting in the same function.
- **The Stepdown Rule**: Organize functions top-to-bottom so the high-level caller function appears first, and the helper functions it calls appear directly below it in call order.
- **Maximum 1–2 Indent Levels**:
  - Use **Guard Clauses** (early `return`, `continue`, or `throw`) at the top of functions to eliminate deep `if/else` nesting.
  - Extract the bodies of loops and conditionals into well-named helper functions.
- **Minimal Arguments (`[F1–F3]`)**:
  - **0, 1, or 2 arguments** is ideal.
  - **3+ arguments**: Group cohesive parameters into a single Argument Object / Options Struct (`CreateUserInput`, `PaginationOptions`).
  - **No Flag Arguments (`[F3, G15]`)**: Never pass a boolean or selector enum just to switch behavior inside a function. Split `render(isSuite: boolean)` into `renderForSuite()` and `renderForSingleTest()`.
  - **No Output Arguments (`[F2]`)**: Return a new value or mutate the owning object rather than mutating input parameters.
- **Command Query Separation (CQS) & Zero Hidden Side Effects**:
  - A function must either **perform an action / change state** (Command) OR **return data** (Query), never both.
  - Never perform hidden state mutations or create hidden temporal couplings (`[G31]`) inside a query/validation function.

#### 3. Self-Documenting Code vs. Comments (`[C1–C5]`)
- **"Don't comment bad code—rewrite it."**
  - Instead of writing a comment over a complex condition, extract a predicate function (`[G28]`) or explanatory variable (`[G19]`):
    - *Bad*: `// Check if user qualifies for discount` + `if (user.tenure > 2 && user.orders >= 10 && !user.isSuspended)`
    - *Clean*: `if (isEligibleForLoyaltyDiscount(user))`
- **Strictly Forbidden Comments**:
  - **Commented-Out Code (`[C5]`)**: Delete immediately; version control remembers history.
  - **Redundant Comments (`[C3]`)**: Comments that merely repeat the function or variable name.
  - **Changelog / Journal Comments (`[C1]`)** and **Closing-Brace Comments** (`} // end while`).
- **When Comments Are Allowed**: Only to explain **why** a non-obvious decision/trade-off was made, warn of consequences (e.g., concurrency/performance constraints), or document public API contracts.

#### 4. Objects, Data Structures & Modularity (`[G23, G36]`)
- **Data/Object Dichotomy**:
  - **Domain Objects / Modules** hide internal state behind abstractions and expose meaningful behaviors (*Tell, Don't Ask*).
  - **Data Structures / DTOs / Records** expose pure data fields with zero business logic.
  - Avoid **Hybrids** that mix public getters/setters for every field with scattered procedural logic.
- **Law of Demeter (`[G36]`)**: Avoid train-wreck chains (`order.getCustomer().getProfile().getAddress().getZipCode()`). Talk only to immediate collaborators.
- **Polymorphism / Strategy over Repeated Switch/If-Else (`[G23]`)**: Follow the **One Switch Rule**—replace repeated type-switching logic with polymorphism, strategy maps, or handler registries.
- **Single Responsibility & High Cohesion**:
  - Every class or module must have **only one reason to change** (describable in ~25 words without `"if"`, `"and"`, `"or"`, or `"but"`).
  - Split a class whenever a subset of methods only operates on a subset of fields.
- **Separate Construction from Use**: Keep dependency wiring, configuration loading, and factory instantiation separate from core runtime business logic.

#### 5. Error Handling & Null Safety
- **Isolate Try/Catch Blocks**: Error handling is *one thing*. Extract happy-path business logic into its own dedicated function so the enclosing `try/catch` function only manages error handling.
- **Exceptions / Explicit Result Types over Error Codes**: Do not use magic error return codes (`-1`, `false`) that callers can silently ignore.
- **Wrap Third-Party APIs**: Encapsulate external libraries/SDKs behind thin domain adapters to translate vendor errors into domain errors and decouple application code from vendor interfaces.
- **Never Return `null` & Never Pass `null`**:
  - Return empty collections (`[]`), **Special Case / Null Objects**, or explicit `Optional` / `Result` types instead of `null`.
  - Reject or default `null` inputs at system boundaries so internal functions never need defensive `if (x != null)` clutter.

---

### Step 3: Successive Refinement ("First Make It Work, Then Make It Right")
1. Never stop at the "first working draft."
2. Before delivering code, run an iterative **Clean Code Refinement Pass**:
   - Break down any function >20 lines into single-abstraction helpers.
   - Replace generic names (`data`, `res`, `temp`, `flag`, `item`) with precise domain names.
   - Eliminate duplicate blocks (`[G5]` DRY).
   - Remove dead code (`[G9, F4]`), unused imports (`[J1]`), and unnecessary comments (`[C3, C5]`).

---

### Step 4: Pre-Delivery 10-Point Clean Code Checklist
Verify every file you create or edit against these 10 checkpoints:
- [ ] **1. Intention-Revealing Names (`[N1–N7]`)**: Zero noise words (`data`, `info`, `temp`), zero encodings, zero magic numbers (`[G25]`).
- [ ] **2. Small Functions (`[G30, G34]`)**: Every function does *one thing* at *one level of abstraction* (<20 lines, <=2 indent levels).
- [ ] **3. Stepdown Rule**: High-level entry points at the top; private helpers directly below their callers.
- [ ] **4. Clean Arguments (`[F1–F3]`)**: 0–2 arguments (or Argument Object), zero boolean flag arguments, zero output arguments.
- [ ] **5. CQS & No Side Effects**: State mutations (Commands) are strictly separated from data queries (Queries).
- [ ] **6. DRY (`[G5]`)**: Zero duplicated logic, validation, or conditional chains.
- [ ] **7. Law of Demeter (`[G36]`) & SRP**: No train wrecks (`a.b().c()`), high cohesion, one reason to change per module/class.
- [ ] **8. Clean Error Handling**: `try/catch` blocks are extracted from business logic; never return or pass raw `null`.
- [ ] **9. Zero Comment Clutter (`[C1–C5]`)**: No commented-out code, no redundant comments; complex conditionals extracted into named predicates (`[G28]`).
- [ ] **10. Boy Scout Rule**: The codebase is cleaner, simpler, and more efficient than before you touched it.
