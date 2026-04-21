# Coding Style and Conventions Summary

## Naming Conventions
- **Standalone Helper Functions and Variables**:
  - Use `snake_case` for function names and their associated variables.
  - Examples: `compute_distance`, `find_element`, `min_value`, `max_value`, `current_pos`, `result`.
- **Class Methods and Member Variables**:
  - Use `gamelCase` (camelCase, where the first letter is lowercase and subsequent words start with an uppercase letter) for class method names and member variable names.
  - Examples: `getPosition`, `setVelocity`, `currentTime`, `maxSpeed`.
- **Class Names**:
  - Use `UpperCaseFirst` (PascalCase, where each word starts with an uppercase letter).
  - Example: `DataPoint`, `TimeSeries`.
  - **File Naming**: The header file name must match the class name exactly, with `.hpp` extension.
  - Example: Class `TcpServer` should be in file `TcpServer.hpp`, class `TcpEventListener` in `TcpEventListener.hpp`.

## Template Typenames
- Use `T` for the generic data structure type.
- Use `TKey` for the key type used in comparisons (e.g., for sorting or searching).

## Function and Parameter Names
- Specific function names requested for helper functions:
  - `linear_interpolation` for a function that estimates an index position.
  - `linear_interpolation_search` for a search function using interpolation.
- The function parameter for extracting a key value is named `getter`.

## Variable Names
- Specific variable names for helper functions:
  - `l` for the lower index bound.
  - `h` for the higher index bound.
  - `low` for the key at the lower bound.
  - `high` for the key at the higher bound.
  - `actual` for the key at the estimated position.
  - `getter` for the function parameter that extracts the key.

## Namespace Usage
- Use `using namespace std;` to avoid `std::` prefixes for standard library components (e.g., `vector`, `function`, `cout`, `endl`).
- Note: This is applied in header files, though it may cause namespace pollution in larger projects.

## Block Style
- For single-line `if` statements, omit curly braces `{}` to keep the code concise.
  - Example: `if (x == y) return x;` instead of `if (x == y) { return x; }`.

## Casting Style
- Use C-style casting `(typename)` instead of C++ `static_cast`.
  - Example: `(double)(x - y)` instead of `static_cast<double>(x - y)`.

## Code Structure
- The code is designed as a header file containing template helper functions for tasks like searching or index estimation.
- Functions assume input data is sorted by the key and roughly uniformly distributed for optimal performance.
- A getter function (passed as a `function<TKey(const T&)>`) extracts the key for comparisons.
- The code handles edge cases like empty containers, single-element containers, and out-of-bounds estimates.

## Example Code
To illustrate the class-related conventions, consider a class representing a data point with a timestamp and value:

```cpp
#include <vector>
#include <functional>

using namespace std;

class DataPoint {
public:
    int getTimestamp() const { return currentTime; }
    int getValue() const { return dataValue; }
private:
    int currentTime; // gamelCase for member variable
    int dataValue;   // gamelCase for member variable
};

// Example helper function using snake_case
template<typename T, typename TKey>
int linear_interpolation_search(const vector<T>& data, TKey target, function<TKey(const T&)> getter) {
    if (data.empty())
        return -1;

    int l = 0;
    int h = data.size() - 1;

    // Simplified body for illustration
    return -1; // Placeholder
}

int main() {
    vector<DataPoint> data = { /* Initialize DataPoint objects */ };
    auto getter = [](const DataPoint& p) { return p.getTimestamp(); };
    int index = linear_interpolation_search(data, 1000, getter);
    cout << "Found index: " << index << endl;
    return 0;
}
```

## Notes
- The `snake_case` convention applies only to standalone helper functions and their variables, as specified.
- The `gamelCase` convention (corrected from `gameCase`) applies to class methods and member variables, ensuring consistency within class definitions.
- The `UpperCaseFirst` convention for class names aligns with common C++ practices (e.g., `DataPoint`).
- Using `using namespace std;` in header files may lead to namespace pollution in larger projects, but it is included as requested.
- C-style casting `(typename)` is less type-safe than `static_cast` but is used as specified.
- The conventions ensure clarity and consistency while supporting reusable, generic code for tasks like searching or index estimation.
- These are in-house conventions for some backward compatibility and technical reasons, and it's important to follow them.
- For single-line `if`, `for`, and `while` statements, omit curly braces `{}` to keep the code concise.
- The standard compilation command is `${workspaceFolder}/build <source_file> <options>`, where options include `--debug`, `--fast`, `--strict`, and `--test`.


## Guard Clauses (Elseless / Early Return Pattern)

Use **guard clauses** (also called *early returns*) instead of deeply nested if-else chains. Place validation checks at the top of functions that immediately return when conditions aren't met—this "fails fast" before doing any real work.

### Benefits

| Benefit | Description |
|---------|-------------|
| Reduced cognitive load | Reader handles error cases first, then focuses on happy path |
| Less indentation | Main logic stays un-nested |
| Clearer intent | Validates BEFORE processing |

### Example - Without Guard Clauses (Nested)

```cpp
void process(User* user) {
    if (user != nullptr) {
        if (user->isActive()) {
            // ... actual logic here ...
        }
    }
}
```

### Example - With Guard Clauses (Early Returns)

```cpp
void process(User* user) {
    if (!user) return;              // Guard #1 - fail fast!
    if (!user->isActive()) return;  // Guard #2
    
    // No deep nesting needed for main logic...
}
```


**Rule**: Libraries should NEVER set up signal handlers directly using `sigaction()`.

**Why**: 
- Signal handlers are global to the process
- Multiple libraries setting handlers would overwrite each other
- The main application loses control over signal handling

