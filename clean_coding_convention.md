## 1. One Top-Level Type Per File

**Goal:** Only one top-level type definition (`class`, `struct`, `interface`, etc.) should exist in each source file.

---

## 2.1 Single Responsibility Principle

**Goal:** Each file / module should have a single, well-defined responsibility. Large files or recent changes that define multiple responsibilities should be refactored.

Review large files for potential multi-responsibility modules.

## 2.2 Separation of Concerns

**Goal:** Each folder should contain file(s) for only one abstraction/responsibility/concern/intent.

Review folders with many files; if there is a clear separation among them, categorize them into subfolders.

---

## 3. Elseless / Early-Return Guarded Code

**Goal:** Prefer guard clauses with early returns over nested `if/else`. Also, throw errors instead of returning error codes when possible.

**Error-throwing convention:** No linter can fully enforce "raise/throw instead of return error codes" because that is semantic. However, a regex-based pre-commit hook could flag suspicious patterns (e.g., returning null/falsy/default-empty values in functions expected to throw):
```bash
grep -rnP '^\s+return\s+(null|None|nullptr|\[\]|{}|false|False)' src/ --include="*.{py,ts,js,cpp,hpp,h}" | grep -v '# noqa'
```

**Summary:** The "throw instead of return errors" convention should be reinforced via code review and optionally a regex check. Refactor for recent changes if necessary.

---

## 4. DRY — Don't Repeat Yourself

**Goal:** Write reusable code; avoid copy-paste duplication by extracting shared logic into functions/modules and calling them instead of duplicating.

---

## 5. Consistency in File Names

**Goal:** File names, type/class/struct names, function names, and other identifiers should be consistent, making it easier to find and understand the code.

File names correlate with the primary name they contain:
- For example a `ConsoleReader` class/type should live in a file named `ConsoleReader.ts`, `console_reader.py`, or `ConsoleReader.cpp` — but never in `reading_from_console.cpp`.
- Same for functions: if `console_reader()` is the main concern of the file, it should be called `console_reader.{ext}`.

