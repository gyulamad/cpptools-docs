## 1. One class per file

**Goal:** Only one top-level `class` definition should exist in each `.py` file.

---

## 2.1 Single Responsibility Principle

**Goal:** Each file / module should have a single, well-defined responsibility. Large files or recent changes that define multiple responsibilities should be refactored.

Review large files for potential multi-responsibility modules.

## 2.2 Separation of concerns

**Goal:** Each folder should contains file(s) for only one abstraction/responsigility/consern/intent

Review folder with many files and if the there are clear separation of the files categorize them into subfolders

---

## 3. Elseless / early return guarded code

**Goal:** Prefer guard clauses with early returns over nested `if/else`. Also, throw errors instead of returning error codes when possible.

**Error-throwing convention:** No linter can fully enforce "raise instead of return error codes" because that is semantic. However a regex-based pre-commit hook could flag patterns like `return None` in functions expected to raise:
```bash
grep -rnP '^\s+return (None|False|\[\]|{})' src/ --include="*.py" | grep -v '# noqa'
```

**Summary:** The "throw instead of return errors" convention should be reinforced via code review and optionally a regex check, refactor for recent changes if necessary.

---

## 4. DRY — Don't Repeat Yourself

**Goal:** Write reusable code; avoid copy-paste duplication by extracting shared logic into functions/modules and calling them instead of duplicating.

---

## 5. Consistency in file names

**Goal:** Files/class names/function names and other identidiers are consistents, makes easier to find and understand the code.

File names correlates the class name that it contains:
For example a ConsoleReader class should be in a file named ConsolReader.py (or consol_reader.py) but never in a reading_from_console.py
Same for function names if a console_reader() function is the main concern of the file it should called console_reader.py
