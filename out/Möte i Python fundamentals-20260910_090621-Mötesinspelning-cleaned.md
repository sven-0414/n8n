## Python Fundamentals: Control Flow and Loops

### Comparisons and Boolean Expressions

- **Definition**: Comparisons in Python evaluate to a boolean value (True or False).
- **Operators**:
  - `==` Equality
  - `!=` Not equal
  - `>` Greater than
  - `<` Less than
  - `>=` Greater than or equal to
  - `<=` Less than or equal to
- **Example**:
  ```python
  print(5 > 2)  # Output: True
  print(5 < 2)  # Output: False
  print(5 == 2) # Output: False
  print(5 != 2) # Output: True
  print(5 >= 2) # Output: True
  print(5 <= 2) # Output: False
  ```
- **Gotchas**: 
  - Be careful with the use of `=` (assignment) versus `==` (comparison).
  - `1 == '1'` evaluates to `True` in Python due to type coercion, but it's generally a bad practice.

### Conditional Statements

- **Syntax**: 
  - `if condition:`
  - `elif condition:`
  - `else:`
- **Indentation**: 
  - Python uses indentation to define code blocks. A common practice is to use 4 spaces per indentation level.
- **Example**:
  ```python
  age = 18
  if age >= 18:
      print("Adult")
  else:
      print("Under 18")
  ```
- **Logical Operators**:
  - `and` (both conditions must be true)
  - `or` (at least one condition must be true)
  - `not` (reverses a boolean value)
- **Example**:
  ```python
  age = 25
  has_ticket = True
  if age >= 18 and has_ticket:
      print("You may enter")
  else:
      print("Entry denied")
  ```

### Truthiness and Falsiness

- **Definition**: Some values are inherently treated as `True` or `False` in boolean contexts.
- **Falsy Values**:
  - `None`
  - `False`
  - `0` (integer or float)
  - Empty sequences (strings, lists, dictionaries, etc.)
- **Example**:
  ```python
  name = ''
  if name:
      print("Name is not empty")
  else:
      print("Name is empty")
  ```
- **Gotchas**: 
  - `False` is falsy, but `True` is truthy.
  - Strings with spaces (like `' '` or `'    '`) are truthy.

### Loops

#### For Loop

- **Syntax**:
  - `for variable in iterable:`
  - `print(variable)`
- **Example**:
  ```python
  languages = ['Python', 'Java', 'C#']
  for language in languages:
      print(language)
  ```
- **Nested Iteration**:
  - Use `for` loops to iterate over dictionaries and lists.
  - Example:
    ```python
    student = {'name': 'Ada', 'age': 25, 'course': 'AI'}
    for key in student:
        print(key, student[key])
    ```
- **Loop Control Statements**:
  - `enumerate()`: Provides index and value of iterable.
    ```python
    for index, language in enumerate(languages):
        print(index, language)
    ```
  - `range(start, stop, step)`: Creates a sequence of numbers.
    ```python
    for number in range(1, 10, 2):
        print(number)
    ```

#### While Loop

- **Syntax**:
  - `while condition:`
  - `print(something)`
- **Example**:
  ```python
  count = 1
  while count <= 5:
      print(count)
      count += 1
  ```
- **Infinite Loops**:
  - Be cautious of conditions that never become false.
  - Example of an infinite loop:
    ```python
    count = 1
    while count <= 5:
        print(count)
    ```

### Break and Continue Statements

- **Break**:
  - Exits the loop when a condition is met.
  - Example:
    ```python
    numbers = [2, 4, 6, 7, 8, 10]
    for number in numbers:
        if number % 2 != 0:
            print("Found an odd number:", number)
            break
    ```
- **Continue**:
  - Skips the rest of the current iteration and moves to the next one.
  - Example:
    ```python
    numbers = [1, 2, 3, 4, 5]
    for number in numbers:
        if number == 3:
            continue
        print(number)
    ```

### Summary

Today's lesson covered control flow statements, loops, and loop control mechanisms in Python. Understanding these concepts is crucial for writing efficient and readable code. Practice these techniques to solidify your understanding.