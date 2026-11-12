# Clean Code Global Standard

Always enforce these Clean Code principles across every language, framework, and project. Whenever writing, modifying, reviewing, or refactoring code, activate and follow the `clean-code` skill.

## 1. Core Philosophy & Simple Design
- **The Boy Scout Rule**: Always leave the code cleaner than you found it.
- **Readability First (10:1 Ratio)**: Code is read 10x more than it is written. Optimize for clarity, simplicity, and directness over cleverness.
- **Four Rules of Simple Design (in priority order)**:
  1. **Runs all tests**: Systems must be testable and verified.
  2. **Contains no duplication (`[G5]` DRY)**: Eliminate duplicate data, logic, and algorithms.
  3. **Expresses programmer intent**: Code should read like well-written prose.
  4. **Minimizes classes and methods**: Avoid over-engineering, premature abstraction, and Big Design Up Front (BDUF). Use the simplest solution that works.

## 2. Meaningful Names (`[N1–N7]`)
- **Intention-Revealing**: Names must state *why* something exists, *what* it does, and *how* it is used (`elapsedTimeInDays`, `getFlaggedCells()`).
- **No Disinformation or Noise**: Avoid `data`, `info`, `object`, `variable`, `list` (unless actually a List), or number-series (`a1, a2`).
- **Searchable & Pronounceable**: Replace magic numbers/strings with named constants (`[G25]`). Scope length dictates name length (`i, j` only in tiny loops `[N5]`).
- **No Encodings (`[N6]`)**: No Hungarian notation (`strName`), no member prefixes (`m_`), no `I` prefix on interfaces (`PaymentGateway`, not `IPaymentGateway`).
- **Parts of Speech**: Classes/Objects = Nouns (`Customer`, `AddressParser`; avoid `Manager`, `Processor`, `Data`). Methods/Functions = Verbs (`postPayment`, `isValid`).
- **One Word per Concept**: Do not mix `fetch`, `retrieve`, and `get` for the same concept across a codebase.

## 3. Small, Focused Functions (`[F1–F4, G30, G34]`)
- **Small!**: Keep functions short (ideally 5–15 lines, rarely >20 lines). Max indentation level: 1–2 (use guard clauses / early returns).
- **Do One Thing (`[G30]`)**: A function must only execute steps one level of abstraction below its name (`[G34]`).
- **The Stepdown Rule**: Organize code top-to-bottom so callers appear above callees (`TO` paragraphs).
- **Minimal Arguments (`[F1]`)**: Prefer 0 (niladic), 1 (monadic), or 2 (dyadic) arguments. Avoid 3+ arguments by grouping cohesive parameters into an Argument Object.
- **No Flag Arguments (`[F3, G15]`)**: Never pass boolean/enum selector flags that make a function do two different things; split into separate explicit functions.
- **No Side Effects or Output Arguments (`[F2]`)**: Never make hidden state changes. Follow **Command Query Separation (CQS)**—a function either changes state (Command) or returns data (Query), never both.

## 4. Self-Documenting Code & Comments (`[C1–C5]`)
- **"Don't comment bad code—rewrite it."** Extract explanatory variables (`[G19]`) and well-named predicate functions (`[G28]`) instead of writing comments.
- **Forbidden Comments**: Never leave commented-out code (`[C5]`), redundant comments (`[C3]`), changelog/journal comments (`[C1]`), closing-brace comments, or noise comments.
- **Allowed Comments**: Only write comments to explain *intent/why* (non-obvious architectural or algorithmic trade-offs), legal notices, warnings of consequences, or public API docs.

## 5. Formatting & Organization (`[G10]`)
- **Newspaper Metaphor**: Keep files concise (~200 lines, <500 max). High-level public APIs at the top, private implementation details directly below their callers.
- **Vertical Distance**: Declare local variables right above their first use. Keep conceptually related functions vertically close.
- **Horizontal Clarity**: Keep lines under 100–120 characters. Never horizontally align declarations or collapse scopes into unreadable one-liners.

## 6. Objects, Classes & Architecture (`[G23, G36]`)
- **Single Responsibility Principle (SRP)**: A class/module must have only one reason to change and be describable in ~25 words without "and", "or", "if", or "but".
- **High Cohesion**: Split classes when a subset of methods only operates on a subset of fields.
- **Law of Demeter (`[G36]`)**: Avoid train wrecks (`a.getB().getC().doSomething()`). Tell objects what to do; don't ask for their internals to manipulate them (`Feature Envy [G14]`).
- **One Switch Rule (`[G23]`)**: Replace repetitive `switch`/`if-else` type dispatch with polymorphism, keeping at most one factory switch to instantiate polymorphic objects.
- **Separate Construction from Use**: Keep dependency wiring/factories separate from clean domain logic.

## 7. Error Handling, Boundaries & Tests (`[T1–T9]`)
- **Exceptions over Error Codes**: Extract `try/catch/finally` blocks into dedicated functions so error handling is not mixed with business logic.
- **Never Return `null` & Never Pass `null`**: Return empty collections, `Optional`/nullable result types, or **Special Case Objects** instead of `null`.
- **Clean Boundaries**: Wrap third-party APIs/SDKs behind domain adapters to localize dependencies and translate vendor exceptions.
- **F.I.R.S.T. Clean Tests**: Keep unit tests as clean as production code using **Build-Operate-Check** (Arrange-Act-Assert), one concept per test, and Fast, Independent, Repeatable, Self-Validating, Timely execution.
