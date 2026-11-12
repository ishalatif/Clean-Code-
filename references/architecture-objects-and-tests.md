# Reference: Objects, Error Handling, Boundaries, Tests, Classes, Systems & Concurrency

Applicable across all programming languages and architectures.

---

## 1. Objects and Data Structures

### Data Abstraction
Hiding implementation is not just putting a layer of getters and setters between variables. True abstraction exposes **abstract interfaces** that allow callers to manipulate the *essence* of the data without knowing its internal representation.

```ts
// Bad (Concrete Vehicle — exposes implementation details):
interface Vehicle {
  getFuelTankCapacityInGallons(): number;
  getGallonsOfGasoline(): number;
}

// Clean (Abstract Vehicle — hides representation):
interface Vehicle {
  getPercentFuelRemaining(): number;
}
```
Do not blindly add getters and setters. Prefer telling the object to perform an operation on its own data.

---

### Data/Object Anti-Symmetry
Objects and Data Structures are virtual opposites:
- **Objects** hide their data behind abstractions and expose functions that operate on that data.
- **Data Structures** expose their data and have no meaningful functions.

| Approach | Easy to Add... | Hard to Add... |
|---|---|---|
| **Procedural / Data Structures** | New **functions** without changing existing data structures | New **data structures** (because every function must be updated) |
| **Object-Oriented (Polymorphic Objects)** | New **classes/types** without changing existing functions | New **functions** (because every class must be updated) |

- **Rule**: Use **Objects** when you are likely to add new data types/variants. Use **Data Structures** when you have a fixed set of data types and frequently add new operations/functions over them.
- **Avoid Hybrids**: Half-object, half-data-structure classes (with public fields or getters/setters for everything mixed with business functions) give you the worst of both worlds.

---

### The Law of Demeter (`[G36]`) & Train Wrecks
A method `f` of a class `C` should only call methods of:
1. `C` itself
2. An object created by `f`
3. An object passed as an argument to `f`
4. An object held in an instance variable of `C`

It must **not** call methods on objects returned by any of the allowed functions (*"Talk to friends, not to strangers"*).

```ts
// Bad (Train Wreck violating Law of Demeter):
const outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();

// Clean (Tell the object what to do instead of asking for internals):
const bos = ctxt.createScratchFileStream(classFileName);
```
*(Note: If `ctxt`, `options`, and `scratchDir` are pure Data Structures with no behavior, `ctxt.options.scratchDir.absolutePath` does not violate Demeter because there is no behavior encapsulation being breached.)*

---

### DTOs and Active Records
- **DTOs (Data Transfer Objects)**: Structures/records with public fields and no business functions, used to communicate with databases, sockets, or API payloads.
- **Active Records**: Special DTOs with navigational methods like `save` and `find`. Treat Active Records strictly as **data structures**, and put business rules in separate **Domain Objects** that hold internal references to the Active Records.

---

## 2. Error Handling

### Write Your `Try-Catch-Finally` Statement First
`try` blocks are like transactions: `catch` must leave your program in a consistent state no matter what happens in the `try`. Writing the `try-catch-finally` first helps you define what the caller should expect. Write a test that forces the exception, then add the handler behavior.

### Use Unchecked Exceptions (or Clean Result Types)
Checked exceptions violate the **Open/Closed Principle (OCP)**: if you throw a checked exception from a low-level method and the `catch` is three levels above, every intermediate method signature must declare that exception—breaking encapsulation.

### Provide Context with Exceptions
Pass informative context with every error: mention the **operation that failed** and the **type of failure** (e.g., `"Unable to open scratch file: " + filePath`).

### Define Exception Classes in Terms of a Caller's Needs (Wrapping Third-Party APIs)
Instead of catching 5 different vendor exceptions everywhere you call a third-party API, **wrap the third-party API** in a thin domain adapter:

```ts
export class LocalPort {
  constructor(private readonly innerPort: ACMEPort) {}

  open(): void {
    try {
      this.innerPort.open();
    } catch (error) {
      throw new PortDeviceFailureError("Failed to open local port", { cause: error });
    }
  }
}
```
Wrapping third-party APIs:
1. Minimizes dependencies on the vendor library.
2. Makes mocking in unit tests trivial.
3. Lets you swap vendors in the future with zero changes to domain callers.

---

### Define the Normal Flow (Special Case Pattern)
Don't use exceptions for normal business control flow.

```ts
// Bad (Cluttered with exception-driven business logic):
try {
  const expenses = expenseReportDAO.getMeals(employee.getId());
  total += expenses.getTotal();
} catch (error) {
  if (error instanceof MealExpensesNotFoundError) {
    total += getMealPerDiem();
  }
}

// Clean (Special Case Pattern — expenseReportDAO returns PerDiemMealExpenses when none found):
const expenses = expenseReportDAO.getMeals(employee.getId());
total += expenses.getTotal();
```

---

### Don't Return `Null` & Don't Pass `Null`
- **Every `if (x != null)` check is a code smell.** When you return `null`, you create work for yourself and foist problems upon your callers; a single missing null check blows up the application.
- **Instead of returning `null`**:
  1. Return an **empty collection** (`[]`).
  2. Return a **Special Case / Null Object** (`GuestUser`, `EmptyOrder`).
  3. Throw a domain exception or return an explicit `Optional<T>` / `Result<T, E>` type.
- **Never Pass `Null`**: Returning `null` is bad, but passing `null` into functions is worse. Forbid `null` arguments by default.

---

## 3. Boundaries

### Encapsulate Third-Party Boundaries
Do not pass raw boundary interfaces (like untyped `Map<string, any>` or raw third-party SDK objects) around your system. Wrap them inside a focused domain class (`Sensors`) that exposes only the exact methods and types your application needs:
```ts
export class Sensors {
  private readonly sensors = new Map<string, Sensor>();

  getById(id: string): Sensor | undefined {
    return this.sensors.get(id);
  }
}
```
Now business rules and type safety are enforced in **one** place instead of scattered across every caller.

### Learning Tests & Adapter Pattern
- **Learning Tests**: Instead of experimenting with a new third-party package directly in production code, write isolated unit tests that verify how the third-party API behaves. When the vendor releases a new version, run your learning tests first to detect behavioral regressions immediately.
- **Code That Does Not Yet Exist**: When an external subsystem or API is undefined, define the interface **you wish you had** from the caller's perspective, test against a Fake implementation, and build an **Adapter** once the external API is ready.

---

## 4. Unit Tests

### The Three Laws of TDD
1. **First Law**: Do not write production code until you have written a failing unit test.
2. **Second Law**: Do not write more of a unit test than is sufficient to fail, and not compiling is failing.
3. **Third Law**: Do not write more production code than is sufficient to pass the currently failing test.

### Keep Tests Clean!
> **Test code is just as important as production code.**

If tests are dirty, copy-pasted, and unreadable, they become harder to change than the production code. **Unit tests are what keep your code flexible, maintainable, and reusable.**

### Clean Tests = Readability (Build-Operate-Check)
What makes a test clean? **Clarity, simplicity, and density of expression.**
- Structure every test using the **BUILD-OPERATE-CHECK** (Arrange-Act-Assert / Given-When-Then) pattern:
  1. **BUILD**: Construct the test data using clean helper methods.
  2. **OPERATE**: Call the function under test.
  3. **CHECK**: Assert that the operation produced the expected results.
- **Domain-Specific Testing Language**: Refactor noisy setup and assertion details into expressive helper functions (`makePages("PageOne", "PageTwo")`, `submitRequest("root", "type:pages")`, `assertResponseIsXML()`, `assertResponseContains(...)`).

### One Concept per Test
- Minimize the number of assertions per concept, and **never test multiple independent concepts in the same test function**. Split monolithic test functions into focused tests.

### F.I.R.S.T. Rules of Clean Tests
- **F — Fast**: Tests should run quickly so you run them constantly (`[T9]`).
- **I — Independent**: Tests must not depend on each other or require a specific execution order.
- **R — Repeatable**: Tests must be repeatable in any environment (laptop, CI, offline without network/DB).
- **S — Self-Validating**: Tests must have a boolean output (pass or fail)—never require manual log inspection.
- **T — Timely**: Write unit tests right *before* the production code that makes them pass.

---

## 5. Classes & Modules

### Class Organization
Follow standard order from top to bottom:
1. Public static constants
2. Private static variables
3. Private instance variables
4. Public functions
5. Private utility functions called by a public function (placed right below the public function per the **Stepdown Rule**)

### Classes Should Be Small!
With functions, we measure size by physical lines. With classes/modules, we measure size by **responsibilities**.
- The name of a class must describe what responsibilities it fulfills. If you cannot derive a concise name (or have to resort to weasel words like `Processor`, `Manager`, `Super`), the class is too big.
- **The 25-Word Test**: You should be able to write a brief description of the class in ~25 words **without using the words `"if"`, `"and"`, `"or"`, or `"but"`**.

### The Single Responsibility Principle (SRP)
> **A class or module should have one, and only one, *reason to change*.**

Do not fear having many small, single-purpose classes/modules. A system with many small, well-named modules has no more moving parts than a system with a few huge monoliths—it simply organizes tools into clearly labeled drawers instead of dumping everything into two giant boxes.

### Cohesion
- Classes should have a small number of instance variables, and each method of a class should manipulate one or more of those variables.
- **Maintaining Cohesion Results in Many Small Classes**: When a class loses cohesion because some variables are only used by a subset of methods, **split the class** so each new class has maximum cohesion.

### Organizing for Change (OCP) & Isolating from Change (DIP)
- **Open-Closed Principle (OCP)**: Modules should be open for extension (by adding new implementations/subclasses) and closed for modification (existing tested modules don't need to be edited when adding a new behavior variant).
- **Dependency Inversion Principle (DIP)**: High-level modules should depend upon **abstractions** (`StockExchange` interface), not concrete details (`TokyoStockExchange` implementation). This keeps business rules testable in complete isolation.

---

## 6. Systems

### Separate Constructing a System from Using It
Software systems must separate the **startup/construction process** (when application objects are constructed and dependencies are wired together) from the **runtime logic** (after startup):
- **Never mix lazy-initialization construction idioms with runtime business logic.**
- **Separation of Main**: Keep all construction in `main` (or bootstrap modules) and hand over the constructed graph to the application.
- **Factories**: When the application must decide *when* to create an object at runtime, pass an **Abstract Factory** interface into the domain class so the domain remains ignorant of *how* the concrete object is built.
- **Dependency Injection (DI) / Inversion of Control (IoC)**: Objects should not instantiate their own dependencies; instead, accept dependencies via constructor arguments or parameters.

### Cross-Cutting Concerns & Clean Domain Objects
- Keep domain logic in pure domain objects/functions completely free of persistence, transaction, security, or framework clutter.
- Handle cross-cutting concerns declaratively at the architectural boundary (decorators, middleware, proxies, aspects).

### Emergent Architecture vs. BDUF
- Avoid **Big Design Up Front (BDUF)**. An architecture can grow incrementally from simple to sophisticated as long as you keep concerns strictly separated.
- **Postpone decisions until the last responsible moment** so you make choices with maximum real-world feedback, and **always use the simplest thing that can possibly work**.

---

## 7. Emergence (Four Rules of Simple Design)

A design is "simple" if it follows these four rules (in order of importance):
1. **Runs all the tests**: Making a system testable automatically forces small, single-purpose modules (SRP), loose coupling (DIP), and clean interfaces.
2. **Contains no duplication (`DRY`)**: Duplication is the primary enemy of a well-designed system. Extract tiny helper methods, pull shared skeletons into a **Template Method**, or extract a shared collaborator module.
3. **Expresses the intent of the programmer**: Choose good names, keep functions and modules small, use standard pattern names (`Command`, `Visitor`, `Decorator`), and write expressive unit tests.
4. **Minimizes the number of classes and methods**: Lowest priority of the four. Keep class and method counts small only by avoiding pointless dogmatism (like creating an interface for every single class without reason), never at the expense of tests, DRY, or expressiveness.

---

## 8. Concurrency

### Concurrency Defense Principles
- **Single Responsibility Principle (SRP)**: Keep concurrency-related code strictly separated from other code. Do not mix thread/async lifecycle management directly into domain logic.
- **Corollary — Limit the Scope of Data**: Severely limit access to any data that may be shared across concurrent tasks. Encapsulate shared data and keep critical sections as small as possible.
- **Corollary — Use Copies of Data**: Avoid shared mutable state by using **immutable objects** or copying objects and merging results in a single coordinator.
- **Corollary — Threads/Tasks Should Be as Independent as Possible**: Partition data so each worker operates purely on local variables with zero shared state.

### Know Your Library & Execution Models
- Use thread-safe collections, message queues/channels, and atomic primitives instead of manual locking.
- Understand classic execution models (**Producer-Consumer**, **Readers-Writers**, **Dining Philosophers**) and how to break **Deadlock** (break one of: Mutual Exclusion, Lock & Wait, No Preemption, Circular Wait—e.g., enforce a strict global lock ordering).
- **Prefer Server-Based Locking**: Lock inside the shared server object (or an adapter wrapper) rather than scattering client-based locks across callers.
- **Keep Critical Sections Small** & **Design Graceful Shutdown Early**.

---

## 9. Successive Refinement & Refactoring Workflow

### "First Make It Work, Then Make It Right"
Clean code rarely comes out right on the first try. Write a working draft first, then **relentlessly refactor** it until it reads like prose.
- Leaving "working" code in its messy first-draft state causes the codebase to rot and slows future changes to a crawl.

### Disciplined Incrementalism (TDD + Tiny Steps)
When cleaning up a messy module:
1. **Ensure Test Coverage First**: Before refactoring existing code, verify (and expand) unit test coverage so every behavior is pinned down.
2. **Never Do a Giant Big-Bang Rewrite**: Do not rip apart the whole module at once leaving the code broken for hours.
3. **Move in Tiny Steps**:
   - Extract one variable or one helper method -> Run tests.
   - Move one field or method into a new cohesive class -> Run tests.
   - Replace one conditional branch with polymorphism -> Run tests.
4. Keep the system compiling and passing all tests after **every single step**.
