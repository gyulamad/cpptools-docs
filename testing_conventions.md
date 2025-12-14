# Testing Guidelines and Conventions

## Overview
Tests verify the correctness of functions and class methods, conditionally compiled using a `TEST` macro. They use simple `assert` statements for validation and follow specific naming and structural conventions, including for exception handling.

## Test Function Structure
- **Conditional Compilation**:
  - Test functions are defined and executed only if the `TEST` macro is defined.
  - The `TEST` macro wraps the test function definition.
  - Example:
    ```cpp
    #ifdef TEST
    TEST(test_function_name_case_description) {
        // Test implementation
    }
    #endif
    ```

- **Assertions**:
  - Use `assert(...)` to check test results.
  - Include descriptive messages in assertions to clarify failure reasons.
  - Example: `assert(condition && "Reason for failure");`

## Naming Conventions
- **Test Function Names**:
  - Prefix with `test`, followed by the function or class method name, then a test case descriptor.
  - Use `snake_case` for the test case descriptor and the function/method name, unless the function or method follows a different convention (e.g., `gamelCase` for class methods or `snake_case` for standalone helper functions).
  - Format: `test_<function_or_method_name>_<test_case_descriptor>`.
  - Examples:
    - For a standalone function `compute_distance`: `test_compute_distance_valid_input`.
    - For a class method `getPosition` (class name in `UpperCaseFirst` and function name in `gamelCase`): `test_DataPoint_getPosition_returns_correct_value`.

- **Class and Method Names**:
  - Reflect the naming convention of the function or method being tested.
  - For classes using `UpperCaseFirst` (e.g., `DataPoint`) and methods in `gamelCase` (e.g., `getTimestamp`), the test name respects these conventions in the prefix but uses `snake_case` for the descriptor.
  - Example: `test_DataPoint_getTimestamp_handles_zero`.

### Test Structs
- **When to Use**:
  - Create a test struct only when necessary, such as when test data is complex, shared across multiple test cases, or when avoiding name collisions is critical.
  - Avoid creating a test struct for simple test cases where variables can be defined inline without loss of clarity or risk of naming conflicts.
- **Format When Used**:
  - Use a struct for test data, prefixed with `test_<function_or_method_name>_` followed by `TestData`.
  - Example: `test_compute_distance_TestData` for a function `compute_distance`.
  - The struct should contain fields relevant to the test case, such as input parameters, expected outputs, or configuration data.
- **Purpose**:
  - Test structs help organize related test data, improve readability, and prevent name collisions in complex test scenarios.

#### Example of When to Use a Test Struct
- **Necessary (Use a Struct)**:
  ```cpp
  struct test_IniFile_TestData {
      string filename;
      string file_content;
      unordered_map<string, string> expected_values;
  };

  TEST(test_IniFile_load_valid_file) {
      test_IniFile_TestData data = {
          "test.ini",
          "key1=value1\nkey2=value2",
          {{"key1", "value1"}, {"key2", "value2"}}
      };
      // Test implementation using data
  }
  ```
  *Justification*: The struct is necessary because the test involves multiple related data elements (filename, content, expected key-value pairs) shared across test cases, improving clarity and reducing repetition.

- **Not Necessary (Avoid a Struct)**:
  ```cpp
  TEST(test_compute_distance_simple_case) {
      int x = 5;
      int y = 2;
      assert(compute_distance(x, y) == 3 && "Distance should be 3");
  }
  ```
  *Justification*: The test uses simple variables (`x`, `y`) that are not reused or complex, so a struct would add unnecessary overhead.

## Exception Testing
- **Format for Expected Exceptions**:
  - Use a `try-catch` block to capture exceptions.
  - Set a boolean flag (`threw`) to track if an exception was thrown.
  - Validate the exception message using `str_contains` from `"str_contains.hpp"`.
  - Assert both the exception content and that an exception was thrown.
  - Example:
    ```cpp
    #ifdef TEST
    TEST(test_processData_expected_to_be_thrown) {
        DataPoint obj;
        bool threw = false;
        try {
            obj.processData();
        } catch (exception& e) {
            assert(str_contains(e.what(), "Invalid data") && "Exception should contain 'Invalid data'");
            threw = true;
        }
        assert(threw && "processData should throw because of invalid input");
    }
    #endif
    ```

## Dependencies
- **String Containment**:
  - Use `#include "str_contains.hpp"` for the `str_contains` function to check exception message content, included only when `TEST` is defined.
  - Assumes `str_contains` has the signature `bool str_contains(const string&, const string&);`.

## Example Code
Below is an example demonstrating the testing conventions for a class `DataPoint` with a method `processData` and a standalone function `compute_distance`:

```cpp
#include <string>

using namespace std;

// Example class
class DataPoint {
public:
    void processData() { // gamelCase method
        if (invalid) throw exception("Invalid data");
    }
private:
    bool invalid = true; // gamelCase member
};

// Example helper function
int compute_distance(int a, int b) { // snake_case function
    return abs(a - b);
}

#ifdef TEST
#include "str_contains.hpp"

// Test struct
struct test_compute_distance_TestData {
    int x;
    int y;
};

TEST(test_compute_distance_valid_input) {
    test_compute_distance_TestData data = {5, 2};
    assert(compute_distance(data.x, data.y) == 3 && "Distance between 5 and 2 should be 3");
}

TEST(test_processData_expected_to_be_thrown) {
    DataPoint obj;
    bool threw = false;
    try {
        obj.processData();
    } catch (exception& e) {
        assert(str_contains(e.what(), "Invalid data") && "Exception should contain 'Invalid data'");
        threw = true;
    }
    assert(threw && "processData should throw due to invalid state");
}

#endif
```

## Notes
- The `TEST` macro ensures tests are only compiled when explicitly enabled, keeping production code clean.
- Test struct names (e.g., `test_compute_distance_TestData`) prevent name collisions by including the function/method name prefix.
- The `snake_case` test case descriptors align with the convention for standalone helper functions, while respecting the `gamelCase` or other naming of the tested function/method.
- Exception tests follow a standardized pattern for robust validation of exception occurrence and content.
- The `str_contains.hpp` header is included only under `TEST` to avoid unnecessary dependencies in non-test builds.
- Tests are designed to be simple, focused, and provide immediate feedback via `assert`.
- The `TEST` macro is defined in `utils/misc/TEST.hpp`. It enables/disables test compilation by conditionally defining the macro and including the necessary header files and definitions. When TEST is defined during compilation, the test functions are compiled and added to the tester object. When TEST is not defined, the test functions are not compiled, and the tester object has an empty run method.
- The `str_contains` function is defined in `utils/misc/str_contains.hpp` and is part of the project's utility library. It takes two strings as input and returns true if the second string is a substring of the first string, and false otherwise.
- To see available commands, check the .vscode folder tasks and launch json files.