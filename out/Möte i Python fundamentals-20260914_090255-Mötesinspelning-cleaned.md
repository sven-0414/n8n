## Functions in Python

### Introduction to Functions
A function in Python is reusable code designed to perform a specific task. Instead of writing the same code multiple times, you can define a function and call it whenever needed. This reduces redundancy and makes the code more maintainable.

#### Syntax
To define a function, use the `def` keyword followed by the function name and parentheses. The function body is indented and ends with a colon.

```python
def greet():
    print("Hello")
```

#### Calling a Function
To execute the code within a function, you must call it by its name followed by parentheses.

```python
greet()  # Output: Hello
```

### Function Names
Function names should be lowercase and use snake_case. The name should clearly describe what the function does.

### Parameters and Arguments
Parameters are variables that act as placeholders for the values passed to the function. Arguments are the actual values provided when the function is called.

```python
def greet(name):
    print(f"Hello, {name}")

greet("Ada")  # Output: Hello, Ada
```

### Positional and Keyword Arguments
Positional arguments are specified by their position, while keyword arguments are specified by their name and value.

```python
def introduce(name, age):
    print(f"Name: {name}, Age: {age}")

introduce("Ada", 36)  # Output: Name: Ada, Age: 36
introduce(age=36, name="Ada")  # Output: Name: Ada, Age: 36
```

### Default Parameter Values
You can provide default values for parameters. If no argument is passed, the default value is used.

```python
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}")

greet("Ada")  # Output: Hello, Ada
greet("Ada", "Good morning")  # Output: Good morning, Ada
```

### Returning Values
Use the `return` statement to send a value back from the function to the place where it was called. Unlike `print`, `return` allows further manipulation of the returned value.

```python
def calculate_tax(income, tax_rate):
    return income * tax_rate

result = calculate_tax(50000, 0.3)
print(result)  # Output: 15000
```

### Code After Return
Execution in a function stops after a `return` statement is executed. No code after `return` will run.

```python
def example():
    print("Before return")
    return 10
    print("After return")  # This line will never be executed

result = example()
print(result)  # Output: Before return 10
```

### Combining Functions with Data Structures
Functions can process data stored in lists or dictionaries.

```python
def get_first_item(items):
    return items[0]

languages = ["Python", "Java", "C#"]
print(get_first_item(languages))  # Output: Python
```

### Returning Multiple Values
Python can return multiple values as a tuple.

```python
def min_max(numbers):
    return min(numbers), max(numbers)

numbers = [4, 8, 1, 12, 3]
print(min_max(numbers))  # Output: (1, 12)
```

### Nested Function Calls
Functions can call other functions.

```python
def calculate_tax(income, tax_rate):
    return income * tax_rate

def calculate_income_after_tax(income, tax_rate):
    tax = calculate_tax(income, tax_rate)
    return income - tax

result = calculate_income_after_tax(50000, 0.3)
print(result)  # Output: 35000
```

### Docstrings
Docstrings provide documentation about what a function does.

```python
def calculate_area(width, height):
    """Returns the area of a rectangle."""
    return width * height

print(calculate_area.__doc__)
# Output: Returns the area of a rectangle.
```

### Type Hints
Type hints are optional and improve readability and editor support, but Python does not enforce them at runtime.

```python
def add(a: int, b: int) -> int:
    return a + b

result = add(5, 3)
print(result)  # Output: 8
```

### Summary
- Define functions with `def` to create reusable code.
- Use parameters and arguments to pass data to functions.
- Return values to use results outside the function.
- Understand the differences between `print` and `return`.
- Utilize type hints and docstrings for better documentation and readability.