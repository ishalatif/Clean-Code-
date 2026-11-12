# Reference: Meaningful Names, Functions, Comments & Formatting

Applicable across all programming languages and paradigms.

---

## 1. Meaningful Names

### Use Intention-Revealing Names (`[N1]`)
A variable, function, or class name must answer three questions: **why it exists**, **what it does**, and **how it is used**. If a name requires a comment to explain it, the name fails to reveal its intent.

**Bad (Implicit / Requires Mental Mapping):**
```ts
let d: number; // elapsed time in days

function getThem(theList: number[][]): number[][] {
  const list1: number[][] = [];
  for (const x of theList) {
    if (x[0] === 4) {
      list1.push(x);
    }
  }
  return list1;
}
```

**Clean (Intention-Revealing):**
```ts
let elapsedTimeInDays: number;
let daysSinceCreation: number;

function getFlaggedCells(gameBoard: Cell[]): Cell[] {
  return gameBoard.filter((cell) => cell.isFlagged());
}
```

---

### Avoid Disinformation & Make Meaningful Distinctions
- **No False Clues**: Do not refer to a grouping of accounts as `accountList` unless its type is actually a `List` (and even then, `accounts` or `accountGroup` is better because container types shouldn't be encoded into names).
- **No Near-Identical Names**: Avoid `ControllerForEfficientHandlingOfStrings` vs `ControllerForEfficientStorageOfStrings`.
- **No Number-Series Naming**: Never use `a1, a2, ..., aN` or `source1, source2`. Use `source` and `destination` (or `sourceAccount`, `targetAccount`).
- **No Noise Words**: Words like `Info`, `Data`, `Object`, `Variable`, `Amount`, `Value`, `Manager`, `Processor`, `Helper` are indistinct noise when paired with an existing concept (`ProductInfo` vs `ProductData` vs `Product`).

---

### Use Pronounceable & Searchable Names (`[N5, G25]`)
- **Pronounceable**: `generationTimestamp` and `modificationTimestamp` instead of `genymdhms` and `modymdhms`.
- **Searchable**: Single-letter names and numeric constants cannot be easily searched across a codebase.
  - **The Scope-Length Rule (`[N5]`)**: The length of a variable name must be proportional to the size of its scope. Single-letter variables (`i, j, k`) may **only** be used as loop counters in tiny 3–5 line loops. Wider scopes require descriptive names.
  - **No Magic Numbers (`[G25]`)**: Replace `86400`, `4`, or `'ACTIVE'` with `SECONDS_PER_DAY`, `STATUS_FLAGGED`, or `AccountStatus.ACTIVE`.

---

### Avoid Encodings (`[N6]`)
- **No Hungarian Notation**: Do not prefix or suffix types onto variables (`phoneString`, `ageInt`, `userObj`).
- **No Member Prefixes**: Do not use `m_` or `_` prefixes for class fields (`m_dsc` -> `description`). Keep classes and functions small enough that you don't need prefixes to spot member fields.
- **Interfaces and Implementations**: Do not prefix interfaces with `I` (`ShapeFactory` instead of `IShapeFactory`). If you must distinguish the interface from a single default implementation, encode the implementation (`ShapeFactoryImp` or `DefaultShapeFactory`), not the public interface.

---

### Class Names vs. Method Names
- **Class / Struct / Component Names**: Must be **nouns or noun phrases** (`Customer`, `WikiPage`, `Account`, `AddressParser`). Avoid vague nouns (`Manager`, `Processor`, `Data`, `Info`) and never use verbs.
- **Method / Function Names**: Must be **verbs or verb phrases** (`postPayment`, `deletePage`, `saveOrder`).
- **Accessors, Mutators, Predicates**: Prefix with `get`, `set`, `is`/`has`/`can`.
- **Named Constructors (Static Factory Methods)**: When constructors are overloaded or ambiguous, use static factory methods with names that describe the arguments:
  - Prefer `Complex.fromRealNumber(23.0)` over `new Complex(23.0)`.

---

### Pick One Word per Concept & Don't Pun
- **One Word per Concept**: Do not mix `fetchValue()`, `retrieveValue()`, and `getValue()` across different modules of the same project. Do not mix `DeviceManager`, `ProtocolController`, and `ServiceDriver` if they play the same architectural role.
- **Don't Pun**: Do not use the same word (`add`) for two different semantic purposes (e.g., `add` that sums/concatenates two existing values vs. `add` that appends a single element into a collection—name the latter `insert` or `append`).

---

### Add Meaningful Context (Without Gratuitous Context)
- If variables `firstName`, `lastName`, `street`, `houseNumber`, `city`, `state`, and `zipcode` travel together, don't prefix them with `addrFirstName`, `addrLastName`, `addrState`—group them into an `Address` class/type!
- **Splitting Stateful Functions via Context Class**: If a long function has many interrelated variables, turn the function into a dedicated class (e.g., `GuessStatisticsMessage`) where the shared variables become fields and each sub-step becomes a tiny method.
- **No Gratuitous Context**: Do not prefix every class in a project with project initials (`GSDAccountAddress` -> `AccountAddress`).

---

## 2. Functions

### Small! & Blocks/Indenting
- **Rule 1**: Functions should be small.
- **Rule 2**: Functions should be smaller than that!
- Target **5–15 lines** per function; rarely exceed 20 lines.
- Blocks within `if`, `else`, `while` statements should ideally be **one line long**—usually a call to a well-named helper function. This keeps the enclosing function small and self-documenting because the called function's name explains what the block does.
- Maximum indentation level inside a function should be **1 or 2** (use Guard Clauses / early returns to flatten nested conditionals).

---

### Do One Thing (`[G30]`) & One Level of Abstraction (`[G34]`)
> **FUNCTIONS SHOULD DO ONE THING. THEY SHOULD DO IT WELL. THEY SHOULD DO IT ONLY.**

How to verify if a function does "one thing":
1. **Abstraction Level Check**: All steps inside the function are **one level of abstraction below** the stated name of the function.
   - *Violation*: Mixing high-level concepts (`getHtml()`) with intermediate concepts (`PathParser.render(pagePath)`) and low-level details (`buffer.append("\n")`) inside the same function.
2. **Extraction Test**: If you can extract another function from it with a name that is *not* merely a restatement of its implementation, the original function was doing more than one thing.
3. **Sections Check**: Functions that are divided into visual sections (`// 1. Validate`, `// 2. Transform`, `// 3. Save`) violate "Do One Thing"—extract each section into its own function.

---

### Reading Code from Top to Bottom: The Stepdown Rule
Code should read like a top-down narrative of **`TO` paragraphs**:
- *To include the setups and teardowns, we include setups, then we include the test page content, and then we include the teardowns.*
  - *To include the setups, we include the suite setup if this is a suite, then we include the regular setup.*
    - *To include the suite setup, we search the parent hierarchy for the "SuiteSetUp" page...*

In practice: place the public/high-level function first, followed immediately by the private functions it calls in the order they are called.

---

### Switch Statements & The `ONE SWITCH` Rule (`[G23]`)
Switch statements (and long `if/else if` chains on type codes) inherently do *N* things and violate SRP and OCP.

**Bad (Switch on Type Scattered Across Functions):**
```ts
function calculatePay(e: Employee): Money {
  switch (e.type) {
    case EmployeeType.COMMISSIONED: return calculateCommissionedPay(e);
    case EmployeeType.HOURLY:       return calculateHourlyPay(e);
    case EmployeeType.SALARIED:     return calculateSalariedPay(e);
    default:                        throw new InvalidEmployeeTypeError(e.type);
  }
}
```
*(Problem: `isPayday(e)` and `deliverPay(e)` will duplicate this exact same `switch`!)*

**Clean (Bury the Switch in a Factory + Polymorphism / Strategy):**
```ts
interface Employee {
  isPayday(): boolean;
  calculatePay(): Money;
  deliverPay(pay: Money): void;
}

class EmployeeFactory {
  createEmployee(record: EmployeeRecord): Employee {
    switch (record.type) {
      case EmployeeType.COMMISSIONED: return new CommissionedEmployee(record);
      case EmployeeType.HOURLY:       return new HourlyEmployee(record);
      case EmployeeType.SALARIED:     return new SalariedEmployee(record);
      default:                        throw new InvalidEmployeeTypeError(record.type);
    }
  }
}
```

---

### Function Arguments (`[F1–F3, G15]`)
- **Ideal count is 0 (niladic)**, followed by **1 (monadic)**, then **2 (dyadic)**.
- **3 arguments (triadic)** should be avoided whenever possible; **>3 (polyadic)** requires special justification and should be refactored into an Argument Object.
- **Common Monadic Forms**:
  1. Asking a question about the argument: `fileExists("MyFile"): boolean`.
  2. Transforming the argument and returning a new result: `fileOpen("MyFile"): InputStream`.
  3. Event/Command (mutating system state based on input, where the name clearly reveals it is an event): `passwordAttemptFailedNtimes(attempts: number): void`.
- **Flag Arguments are Forbidden (`[F3, G15]`)**: Passing a boolean into a function loudly proclaims that the function does *at least two things* (one if `true`, another if `false`). Split `render(isSuite: boolean)` into `renderForSuite()` and `renderForSingleTest()`.
- **Argument Objects**: When a function needs 3+ arguments, wrap related arguments into a cohesive object/struct:
  - `makeCircle(x, y, radius)` -> `makeCircle(center: Point, radius: number)`.
- **Verbs and Keywords in Function Names**: Encode argument roles or expected order into the function name: `writeField(name)` or `assertExpectedEqualsActual(expected, actual)`.

---

### Have No Side Effects & Output Arguments (`[F2, G31]`)
Side effects are lies: your function promises to do one thing, but it also does hidden things (modifying class variables, mutating input parameters, or modifying globals).
- **Hidden Temporal Coupling (`[G31]`)**: In `checkPassword(userName, password)`, calling `Session.initialize()` inside `checkPassword` creates a hidden temporal coupling (it can only be called when it is safe to reset the session) and violates "Do One Thing". If they must stay coupled, at least rename it `checkPasswordAndInitializeSession()`.
- **No Output Arguments (`[F2]`)**: Avoid `appendFooter(report)`. Instead, make `appendFooter` a method on the object (`report.appendFooter()`) or return a new value.

---

### Command Query Separation (CQS)
Functions should either **do something** (Command) or **answer something** (Query), never both.

**Bad (Ambiguous Command + Query):**
```ts
function set(attribute: string, value: string): boolean;
// Leads to unreadable caller code:
if (set("username", "admin")) { ... }
```

**Clean (Separated Command and Query):**
```ts
if (attributeExists("username")) {
  setAttribute("username", "admin");
}
```

---

### Prefer Exceptions to Returning Error Codes & Extract Try/Catch Blocks
Returning error codes forces the caller to deal with errors immediately in deeply nested `if` chains (`if (deletePage(page) == E_OK) { if (registry.deleteReference(...) == E_OK) ... }`).

Instead, throw exceptions (or return explicit Result types) and **extract the bodies of `try` and `catch` blocks into their own functions**:

**Clean (Error Handling Separated from Business Logic):**
```ts
function deletePageWithReferences(page: Page): void {
  try {
    deletePageAndAllReferences(page);
  } catch (error) {
    logError(error);
  }
}

function deletePageAndAllReferences(page: Page): void {
  deletePage(page);
  registry.deleteReference(page.name);
  configKeys.deleteKey(page.name.makeKey());
}
```
Notice how `deletePageWithReferences()` is ONLY about error handling, and `deletePageAndAllReferences()` is ONLY about the steps of deleting a page!

---

## 3. Comments (`[C1–C5]`)

### Core Principle
> **Don't comment bad code—rewrite it.**

Comments do not make up for bad code. Code evolves and moves, while comments rot and become lies. Truth can only be found in one place: **the code**.

### Explain Yourself in Code
```ts
// Bad:
// Check to see if the employee is eligible for full benefits
if ((employee.flags & HOURLY_FLAG) && (employee.age > 65))

// Clean:
if (employee.isEligibleForFullBenefits())
```

### When Comments Are Acceptable (Use Sparingly)
1. **Legal Comments**: Copyright and license headers when required.
2. **Explanation of Intent**: Explaining *why* a non-obvious design decision or algorithm was chosen.
3. **Clarification**: Translating the meaning of obscure standard library return values that you cannot alter.
4. **Warning of Consequences**: `// SimpleDateFormat is not thread-safe; instantiate per call.`
5. **Concise `TODO` Comments**: Explaining why a degenerate stub exists and what future event will replace it.
6. **Public API Documentation**: Docstrings for public library interfaces (must remain accurate and non-redundant).

### Bad Comments (Strictly Forbidden)
- **Mumbling**: Vague notes whose meaning only the author knew.
- **Redundant Comments (`[C3]`)**: Comments that take longer to read than the code itself and add zero extra information.
- **Mandated Comments**: Forcing every internal function and parameter to have a docstring creates visual clutter and lies.
- **Journal / Changelog Comments (`[C1]`)**: Source control tracks history; never keep change logs in files.
- **Noise Comments**: `// Default constructor`, `/** The name. */ private name: string;`.
- **Position Markers**: `// Actions //////////////////////////////////`.
- **Closing Brace Comments**: `} // while`, `} // if` — if you feel the need to mark closing braces, your function is too long and deeply nested; extract smaller functions instead!
- **Commented-Out Code (`[C5]`)**: **Delete it immediately!** Source control already remembers it.

---

## 4. Formatting

### Vertical Formatting & The Newspaper Metaphor
- **File Size**: Keep source files small—typically **200 lines**, with an upper limit around **500 lines**. Small files are much easier to understand than 2,000-line monoliths.
- **The Newspaper Metaphor**:
  - **Headline (Top)**: The file/class name and high-level public exports tell you what module you are in.
  - **Synopsis (First Paragraphs)**: Public entry-point functions show the high-level algorithm without drowning you in low-level details.
  - **Details (Bottom)**: Private sub-functions and low-level utilities appear as you scroll down.

### Vertical Openness vs. Vertical Density
- **Vertical Openness**: Separate module declarations, imports, fields, and functions with blank lines. Each blank line is a visual cue that identifies a new and separate concept.
- **Vertical Density**: Lines of code that are tightly related should appear vertically dense (no useless comments or blank lines breaking up related statements).

### Vertical Distance (`[G10]`)
Concepts that are closely related must be kept vertically close to each other in the same file:
- **Variable Declarations**: Declare local variables **as close to their first usage as possible**. Loop control variables should be declared inside the loop statement.
- **Instance Variables**: Declare class fields in one well-known place at the **top of the class**.
- **Dependent Functions**: If one function calls another, they should be vertically close, and the **caller should be placed above the callee** whenever possible.
- **Conceptual Affinity**: Functions that perform similar operations should be grouped together.

### Horizontal Formatting
- **Line Width**: Keep lines short—ideally **80–100 characters**, never exceeding **120 characters**.
- **Horizontal Openness & Density**:
  - Put spaces around assignment and comparison operators to separate left and right sides.
  - Use spacing in expressions to accentuate operator precedence.
- **Avoid Horizontal Alignment**: Never align variable names or `=` signs across multiple lines in columns—it draws the eye away from the true intent and creates diff noise.
- **Don't Break Indentation**: Never collapse short `if`, `while`, or function bodies onto a single unindented line. Always expand and indent the scope body.
