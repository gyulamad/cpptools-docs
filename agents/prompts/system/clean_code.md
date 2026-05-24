# Clean Code Guidelines for Agent AI

## Core Principles

1. **Always follow established patterns** - Study existing code before making changes
2. **Keep it simple** - Prefer readability over cleverness
3. **One responsibility per function/class** - Single Responsibility Principle strictly enforced
4. **DRY (Don't Repeat Yourself)** — Extract common logic into reusable components

5. **Guard clauses over else branches** — Use early returns to eliminate nested `else` blocks entirely; validate preconditions at the top, return on failure, keep happy path flat and unindented

6. **Function complexity limit: max 3-4 flow control constructs per function** — A single function may contain no more than three or four loop/condition/control-flow statements (e.g., `if`, `for`, `while`, `match/case`). If a function needs more, extract sub-routines into private helper functions. Exception: clean enumeration-style switches (`match`/`switch`) are exempt from this count when each case maps directly to an enum or known variant.

### Guard Clauses (Elseless Early Returns)
- **Never use `if/elif/... /else` for flow control when a guard clause applies**. Validate preconditions and edge cases at the top of the function, return early on failure, then write the happy path without indentation. This eliminates deep nesting and makes success logic read sequentially from top to bottom.
- **Pattern**: Check → handle error/failure → `return`; repeat for each precondition. When all guards pass, proceed with main logic at zero indent level — no outer `else` needed or desired.

## Error Handling / No Silent Failures

- **Zero tolerance for silent failures** — Never catch an exception and do nothing with it. Every `except` block must either re-raise, log at a meaningful level, or convert to a domain-specific error that propagates upward. An empty `except: pass` is strictly forbidden (the only acceptable bare `pass` in an except block is inside a deliberately ignored context manager like `contextlib.suppress`).
- **Always surface errors** — If the current layer cannot handle a failure, propagate it with enriched context via chaining (`raise NewError(...) from original_exc`) so the full traceback and intent are preserved.
- **Use specific exception types**, never bare `except:`, and avoid catching overly broad base classes (e.g., don't catch plain `Exception` unless wrapping or re-raising). Use custom domain exceptions where appropriate to make error handling explicit at call sites.
- **Switch/match/case must always have a `default`/`_` branch** — Every exhaustive branching construct includes a final default case that raises an exception (typically an `AssertionError`, `NotImplementedError`, or domain-specific unhandled-case error). This catches bugs in logic even when the enum/value set is believed to be complete. Only omit it if explicitly justified and documented at the call site.

## Code Style Requirements

### Naming Conventions
- Functions/variables: `snake_case` in Python, camelCase in JavaScript
- Classes/types: PascalCase everywhere
- Constants: UPPER_SNAKE_CASE
- Private members prefixed with underscore (`_private_method`)

### Code Organization
- **Multiple implementations for the same interface go into a subfolder named after that interface** — When you have several classes/modules implementing or realizing the same concept, create an implementation subdirectory rather than scattering sibling files at the parent level. For example: if there is a `Peripheral` (or similar) abstraction, place all its concrete variants inside `peripherals/`, yielding structures such as `peripherals/MousePeripheral.py`, `peripherals/KeyboardPeripheral.py`, `peripherals/MicrophonePeripheral.py`, etc. This keeps related implementations visually grouped and signals their shared contract at the filesystem level.
- Group related functionality together (inside those subfolders)
- Keep imports sorted and grouped: stdlib → third-party → local
- No more than 5 levels of nesting maximum - extract when deeper

## Documentation Requirements
- Every public function/class MUST have docstrings explaining purpose, parameters, return values
- Complex logic needs inline comments explaining WHY, not WHAT (the code shows what)

## Architecture for Testability (Dependency Injection)

To write reliable tests **without excessive mocking**, apply these patterns:

- **Encapsulate mutable state in dedicated classes** — Never expose raw lists, dicts, or log buffers as function parameters. Create a `MessageStore` / `HistoryContainer` class that owns the collection and exposes controlled methods (`add(entry)`, `get_snapshot()`, etc.). This prevents accidental overwrites and makes it trivial to inspect state from tests.
- **Abstract external dependencies behind interfaces** — User input, tool executors, LLM clients, file readers — all must be injected via constructor parameters or setter injection as interface objects (abstract base classes / protocols). Tests provide lightweight test implementations; production code provides real ones. Zero mocks needed for behavior validation.
- **Dependency Injection over internal instantiation** — Never call `input()` directly inside business logic. Accept an `InputSource` protocol and inject a `StdinInputProvider` in production, or a list-based iterator for tests. Same pattern applies to history containers: pass them into the constructor of whatever orchestrates turns so tests can assert on real state mutations instead of mock interaction records.
- **Test with real objects, not mocks** — When dependencies are properly injected and encapsulated as interfaces, you should be able to construct testable implementations (e.g., a `ListInputSource`, a `PeekableMessageStore`) that behave exactly like production code but expose inspection methods for assertions. This tests actual logic paths rather than mock call sequences, catching real bugs.

### CI Coverage Threshold — Always Increase, Never Decrease
- **Never lower the coverage threshold in CI.** Once a higher threshold is established and all tests pass at that level, you must adjust the configured threshold upward to match. This keeps relentless pressure on the test suite: every merge that improves (or preserves) coverage should also push the bar up so regressions are caught immediately.
- **How it works**: run `coverage` / your CI test runner → observe the reported percentage → if the result meets or exceeds the current threshold, bump the configured value to this new higher number. If a failure is caused by an actual regression (not merely the threshold), fix the root cause *and* keep the old threshold until all fixes pass — never drop it as a shortcut.

## Architecture Principles

### OOP (Object-Oriented Programming)
- Prefer classes and objects over standalone functions when modeling real-world entities or concepts
- Apply SOLID principles: Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion
- Use encapsulation — hide internal state behind well-defined public interfaces with private/protected members
- Favor composition over inheritance to reduce coupling and increase flexibility

#### Class Composition Limits (OOP)
- **Max 3-4 operational methods per class** — A class may define no more than three or four non-trivial behavior methods. Clean getters, setters, property accessors, `__init__`, `__repr__`/`__str__`, and data-only "bean" methods are excluded from this count. If a class needs more operational logic, split responsibilities into separate classes (often via composition).
- **Max 3-4 instance variables per class** — A class may hold no more than three or four `self.*`/instance attributes at any given time. Excess state indicates the class is managing too many concerns; extract related fields and behavior into collaborator objects injected as dependencies instead.

### No Globals / Module-Level State Variables
- Never use global variables or module-level mutable state across the codebase
- All state must be encapsulated within classes, function scopes, or explicitly managed dependency injection containers
- If shared behavior is needed, pass dependencies as parameters or inject them via constructors — never read/write hidden globals

### No Large Files (Separation of Concerns)
- Keep files focused: if a file exceeds ~200 lines, critically evaluate whether it has too many responsibilities
- Split files along logical boundaries — each concern (e.g., data model, business logic, persistence, API layer) belongs in its own module or class
- One class per file when the class is large enough to warrant isolation; otherwise group only tightly-related small classes together
- Ask: "If someone wanted to reuse this piece of behavior elsewhere, could they copy just one file?" If not, you need more separation.

