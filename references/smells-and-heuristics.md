# Reference: Complete Catalog of 65 Code Smells and Heuristics

Use these codes (`[C1]`, `[G30]`, etc.) during code reviews, refactoring passes, and self-checks across any programming language.

---

## Comments (`C1–C5`)

| Code | Name | Detection Rule & Actionable Fix |
|---|---|---|
| **`C1`** | **Inappropriate Information** | Do not store metadata in comments that belongs in version control, issue trackers, or changelogs (change histories, authors, last-modified dates, ticket numbers). Reserve comments strictly for technical notes about the code and design. |
| **`C2`** | **Obsolete Comment** | A comment that has gotten old, irrelevant, and incorrect. Code moves and evolves; comments cling to the wrong lines if not updated. **Fix**: Delete or update obsolete comments immediately. |
| **`C3`** | **Redundant Comment** | A comment that describes something that adequately describes itself (`i++; // increment i` or `@param sellRequest The SellRequest`). **Fix**: Delete the comment; comments should only say things the code *cannot* say for itself. |
| **`C4`** | **Poorly Written Comment** | A comment worth writing is worth writing well. Don't ramble, state the obvious, or use bad grammar. Keep it brief and concise. |
| **`C5`** | **Commented-Out Code** | Commented-out code rots, confuses readers, and nobody dares delete it. **Fix**: Delete it immediately! Version control remembers every line. |

---

## Environment (`E1–E2`)

| Code | Name | Detection Rule & Actionable Fix |
|---|---|---|
| **`E1`** | **Build Requires More Than One Step** | Building a project should be a single trivial operation (`npm run build`, `mvn package`, `cargo build`). You should never have to manually run scattered scripts or copy files to build. |
| **`E2`** | **Tests Require More Than One Step** | You should be able to run **all** unit tests with a single command (`npm test`, `pytest`, `go test ./...`). Making tests single-step is critical so developers and agents run them constantly. |

---

## Functions (`F1–F4`)

| Code | Name | Detection Rule & Actionable Fix |
|---|---|---|
| **`F1`** | **Too Many Arguments** | Functions should have **0**, **1**, or **2** arguments. Three should be avoided; more than three is unacceptable without wrapping related parameters into an **Argument Object** or making them class fields. |
| **`F2`** | **Output Arguments** | Output arguments are counterintuitive because readers expect arguments to be *inputs*, not *outputs*. **Fix**: If a function must change the state of something, have it return a new value or change the state of the object it is called on (`report.appendFooter()`). |
| **`F3`** | **Flag Arguments** | Boolean arguments loudly declare that the function does more than one thing! **Fix**: Split the function into two explicitly named functions (`renderForSuite()` and `renderForSingleTest()`). |
| **`F4`** | **Dead Function** | Functions or methods that are never called should be **discarded**. Keeping dead code around is wasteful; version control remembers it if you ever need it back. |

---

## General (`G1–G36`)

| Code | Name | Detection Rule & Actionable Fix |
|---|---|---|
| **`G1`** | **Multiple Languages in One Source File** | Minimize the number and extent of extra languages in a single source file (e.g., raw SQL, HTML, XML, regex, and CSS all jammed inside a backend/script file). Extract templates, queries, and styles into dedicated modules/files. |
| **`G2`** | **Obvious Behavior Is Unimplemented** | Following **"The Principle of Least Surprise"**, any function or class should implement the behaviors that another programmer could reasonably expect (e.g., `DayDate.fromString("Monday")` should handle `"monday"` and `"Mon"` without surprising callers). |
| **`G3`** | **Incorrect Behavior at the Boundaries** | Don't rely on intuition! Look for every boundary condition (empty inputs, `0`, negative numbers, `MAX_INT`, off-by-one, leap years, concurrent edge cases) and write explicit tests for all of them. |
| **`G4`** | **Overridden Safeties** | Turning off compiler warnings, linter rules, type checks (`any` / `@ts-ignore`), or skipping failing tests risks endless debugging sessions. Fix the underlying issue instead of silencing the safety check. |
| **`G5`** | **Duplication (DRY)** | Whenever you see the same code structure in more than one place: (1) extract a shared helper method for identical blocks, (2) use **Polymorphism** for repeated `switch/if-else` chains, and (3) use the **Template Method** or **Strategy** pattern for similar algorithms that differ only in a few lines. |
| **`G6`** | **Code at Wrong Level of Abstraction** | High-level general concepts must be in the base class/interface, and lower-level implementation details must be in the derivatives (`percentFull()` belongs on `Stack`, but `MAX_CAPACITY` does not belong on an unbounded `Stack` interface). |
| **`G7`** | **Base Classes Depending on Their Derivatives** | Base classes should know **nothing** about their subclasses/derivatives. If a base class references subclass names or types, invert the dependency via virtual/polymorphic methods. |
| **`G8`** | **Too Much Information** | Well-defined modules have **very small, tight interfaces** that allow you to do a lot with a little. Hide data, utility functions, constants, and temporaries. Don't create classes with dozens of public methods and fields; keep coupling low by limiting information exposure. |
| **`G9`** | **Dead Code** | Impossible `if` conditions, `catch` blocks for exceptions that are never thrown, unused variables, and uncalled methods. **Fix**: Delete dead code from the system immediately. |
| **`G10`** | **Vertical Separation** | Variables and functions should be defined close to where they are used. Local variables should be declared just above their first usage; private functions should be defined just below the first function that calls them. |
| **`G11`** | **Inconsistency** | If you do something a certain way, do all similar things in the **same way** (`processVerificationRequest` -> `processDeletionRequest`, not `handleDeleteReq`). Follow the Principle of Least Surprise. |
| **`G12`** | **Clutter** | Default constructors with no implementation, unused variables, uncalled functions, and zero-information comments are clutter. Keep source files clean, well-organized, and free of clutter. |
| **`G13`** | **Artificial Coupling** | Things that don't depend upon each other should not be artificially coupled (e.g., declaring general `enum`s, constants, or utility functions inside a specific domain class just because it was convenient at the moment). Put functions, constants, and variables in their proper home. |
| **`G14`** | **Feature Envy** | Methods of a class should be interested in the variables and functions of the class they belong to, not the variables and functions of *other* classes (`HourlyPayCalculator` reaching into `HourlyEmployee` for every getter). **Fix**: Move the calculation onto the class that owns the data, or pass a clean abstraction. |
| **`G15`** | **Selector Arguments** | Not just booleans—any argument (enums, integers, strings) used to select the behavior of a function from a list of behaviors is a smell. **Fix**: Have multiple dedicated functions or polymorphism instead of passing selector arguments. |
| **`G16`** | **Obscured Intent** | Code must be as expressive as possible. Avoid run-on expressions, Hungarian notation, and magic numbers (`return m_otCalc.calc(iThsWkd, iThsRte);`). Write code for the human reader first! |
| **`G17`** | **Misplaced Responsibility** | One of the most important decisions is **where to put code**. Follow the **Principle of Least Surprise**: code should be placed where a reader would naturally expect to find it, not where it is merely convenient for the author at the moment. |
| **`G18`** | **Inappropriate Static** | Prefer **nonstatic methods** to static methods when polymorphism or dependency injection may be needed. Only make a function `static` (or a pure standalone helper) if it deals strictly with its arguments, needs no state, and will never need polymorphic substitution. |
| **`G19`** | **Use Explanatory Variables** | Break calculations up into intermediate values held in variables with meaningful names (e.g., assigning regex capture groups to `const key = match[1]; const value = match[2];`). More explanatory variables are almost always better than fewer. |
| **`G20`** | **Function Names Should Say What They Do** | If you have to look at the implementation (or documentation) of a function to know what it does, you must rename it (`date.add(5)` is ambiguous; `date.addDaysTo(5)` or `date.increaseByDays(5)` is clear). |
| **`G21`** | **Understand the Algorithm** | Do not "poke" at the code with `if`s and flags until tests pass. Before you consider yourself done, make sure you truly understand *how* the algorithm works and refactor it until it is obvious to the reader. |
| **`G22`** | **Make Logical Dependencies Physical** | If one module depends upon another, that dependency should be **physical**, not just logical. The dependent module should not make assumptions (like knowing the page size constant of another module); it should explicitly ask the other module for that information (`page.getMaxPageSize()`). |
| **`G23`** | **Prefer Polymorphism to If/Else or Switch/Case** | Before using a `switch`, consider polymorphism. **"ONE SWITCH" Rule**: There may be no more than **one** `switch` statement for a given type of selection, and the cases in that `switch` statement must create polymorphic objects that take the place of other such `switch` statements in the rest of the system. |
| **`G24`** | **Follow Standard Conventions** | Every team/language has coding conventions (camelCase vs snake_case, file structure, formatting). Follow the existing conventions of the codebase and language ecosystem uniformly. |
| **`G25`** | **Replace Magic Numbers with Named Constants** | Never use raw numbers (`86400`, `55`, `0.075`) or magic string literals in code. Hide them behind well-named constants (`SECONDS_PER_DAY`, `LINES_PER_PAGE`), unless the number is self-evident in context (like `2 * Math.PI * radius`). |
| **`G26`** | **Be Precise** | Ambiguities and imprecision in code are the result of laziness. Examples: (1) Never represent currency with floating-point numbers—use integers (cents) or `Decimal`/`BigInt`; (2) If a query returns at most one record, don't return a general list; (3) Don't be lazy about concurrency locks, `null` possibilities, or overly broad `catch` blocks. |
| **`G27`** | **Structure over Convention** | Enforce design decisions with **structure** over convention. Base types/interfaces with required contracts force implementations to provide behavior systematically, whereas a convention of `switch/case` relies on human discipline not to forget a case. |
| **`G28`** | **Encapsulate Conditionals** | Boolean logic is hard enough to understand without seeing it raw inside an `if` or `while`. Extract functions that explain the **intent** of the conditional: `if (shouldBeDeleted(timer))` is much cleaner than `if (timer.hasExpired() && !timer.isRecurrent())`. |
| **`G29`** | **Avoid Negative Conditionals** | Negatives are harder to understand than positives. Prefer `if (buffer.shouldCompact())` over `if (!buffer.shouldNotCompact())`. |
| **`G30`** | **Functions Should Do One Thing** | Functions that have multiple sections executing a series of operations are doing more than one thing. Convert them into smaller functions, each doing one thing at one level of abstraction. |
| **`G31`** | **Hidden Temporal Couplings** | Often functions must be called in a specific order (`dive("moist");` requires calling `saturateGradient()`, then `reticulateSplines()`, then `diveForMoog()`). Expose temporal coupling by creating a **bucket brigade**: each function produces a result that the next function requires as an input argument, making it impossible to call them out of order! |
| **`G32`** | **Don't Be Arbitrary** | Have a reason for the way you structure your code, and make sure that reason is communicated by the structure of the code. Don't declare inner classes or scoped helpers arbitrarily inside an unrelated module. |
| **`G33`** | **Encapsulate Boundary Conditions** | Boundary conditions are hard to keep track of (`level + 1` scattered everywhere). Put the processing for them in **one place** by extracting an explanatory variable: `const nextLevel = level + 1;`. |
| **`G34`** | **Functions Should Descend Only One Level of Abstraction** | Statements within a function should all be written at the same level of abstraction, which should be **one level below** the operation described by the name of the function. Separating levels of abstraction is one of the most important functions of refactoring! |
| **`G35`** | **Keep Configurable Data at High Levels** | If you have a constant such as a default or configuration value that is known and expected at a high level of abstraction, do not bury it in a low-level function. Expose it as an argument to the low-level function called from the high-level function. |
| **`G36`** | **Avoid Transitive Navigation (Law of Demeter)** | In general, a module should know as little as possible about its collaborators ("Writing Shy Code"). Avoid `a.getB().getC().doSomething()`. Instead, have the immediate collaborator provide the service directly: `myB.doSomething()`. |

---

## Language / Type Idioms (`J1–J3`)

| Code | Name | Detection Rule & Actionable Fix |
|---|---|---|
| **`J1`** | **Avoid Long Import Lists / Keep Modules Focused** | Long lists of imports indicate excessive coupling. Keep module dependencies focused and delete unused imports immediately. |
| **`J2`** | **Don't Inherit Constants** | Never put constants in an interface/base class just to "inherit" them into subclasses. Use explicit module exports/imports instead. |
| **`J3`** | **Constants versus Enums** | Don't use raw integer/string constants when the language offers **Enums** or discriminated union types. Enums and rich types provide far stronger expression and compile-time safety than raw constants. |

---

## Names (`N1–N7`)

| Code | Name | Detection Rule & Actionable Fix |
|---|---|---|
| **`N1`** | **Choose Descriptive Names** | Don't be too quick to choose a name (`q`, `s`, `val`). Make sure the name is descriptive, and re-evaluate names continually as the software evolves. Carefully chosen names turn code into a human-readable story. |
| **`N2`** | **Choose Names at the Appropriate Level of Abstraction** | Don't pick names that communicate implementation; choose names that reflect the level of abstraction of the class or function (`Modem.connect(connectionLocator)` instead of `Modem.dial(phoneNumber)`, because not all modems use phone numbers!). |
| **`N3`** | **Use Standard Nomenclature Where Possible** | Names are easier to understand if they are based on existing conventions or design patterns (e.g., name a Decorator `*Decorator`, a Factory `*Factory`, a string converter `toString()`). |
| **`N4`** | **Unambiguous Names** | Choose names that make the workings of a function or variable unambiguous (`renamePageAndOptionallyAllReferences()` instead of `doRename()`). |
| **`N5`** | **Use Long Names for Long Scopes** | The length of a name should be related to the length of the scope. You can use very short variable names (`i`, `j`) for tiny 5-line scopes, but variables or functions with larger scopes **must** have longer, precise names. |
| **`N6`** | **Avoid Encodings** | Names should not be encoded with type or scope information (`m_`, `f`, `str`, `I` on interfaces). |
| **`N7`** | **Names Should Describe Side-Effects** | Names should describe everything that a function, variable, or class is or does. Don't hide side effects with a name (`createOrReturnStream()` instead of `getStream()` if it creates a new stream when none exists). |

---

## Tests (`T1–T9`)

| Code | Name | Detection Rule & Actionable Fix |
|---|---|---|
| **`T1`** | **Insufficient Tests** | A test suite should test everything that could possibly break. As long as there are conditions that have not been explored by the tests or calculations that have not been validated, the tests are insufficient. |
| **`T2`** | **Use a Coverage Tool!** | Coverage tools make it easy to find untested `if/else/catch` blocks by highlighting insufficiently tested modules. |
| **`T3`** | **Don't Skip Trivial Tests** | They are easy to write and their documentary value is higher than the cost to produce them. |
| **`T4`** | **An Ignored Test Is a Question about an Ambiguity** | Expressing ambiguity about requirements using `@Ignore` / `.skip` with an explanation is useful temporarily while waiting for requirement clarification. |
| **`T5`** | **Test Boundary Conditions** | Take special care to test boundary conditions (`0`, empty, max, off-by-one). Algorithms often work in the middle and misbehave at the boundaries. |
| **`T6`** | **Exhaustively Test Near Bugs** | Bugs tend to congregate! When you find a bug in a function, do an exhaustive test of that entire function—you will probably find that the bug was not alone. |
| **`T7`** | **Patterns of Failure Are Revealing** | Sometimes you can diagnose a problem by finding patterns in the way well-ordered, comprehensive test cases fail (e.g., all tests with inputs >5 characters fail). |
| **`T8`** | **Test Coverage Patterns Can Be Revealing** | Looking at the code that is or is not executed by the passing tests gives clues to why the failing tests fail. |
| **`T9`** | **Tests Should Be Fast** | A slow test is a test that won't get run when things get tight. And when tests don't get run, problems aren't found early. Do whatever you must to keep tests fast! |
