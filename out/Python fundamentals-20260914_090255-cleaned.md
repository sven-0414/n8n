# Python Functions

## What is a Function?

A function in Python is reusable code designed to perform a specific task. It allows you to write code once and use it multiple times throughout your program, promoting code reusability and maintainability.

### Code Example:
```python
def greet():
    print("Hello")

greet()  # This calls the function
```

### Key Points:
- Functions help avoid code duplication
- They make programs more organized and readable
- You define a function using the `def` keyword
- Functions can be called multiple times

## Defining Functions

To create a function in Python, you use the `def` keyword followed by the function name, parentheses, and a colon. The function body is indented.

### Code Example:
```python
def greet():
    print("Hello")
    
def calculate_area(width, height):
    area = width * height
    return area
```

## Calling Functions

Functions must be called to execute their code. When calling a function, you must include parentheses after the function name.

### Code Example:
```python
def greet():
    print("Hello")

greet()  # Correct way - calls the function
greet    # Wrong way - this doesn't call the function
```

## Parameters and Arguments

### Parameters
Parameters are variables defined in the function signature. They act as placeholders for values that will be passed to the function.

### Arguments
Arguments are the actual values passed to a function when it's called.

### Code Example:
```python
def greet(name):  # 'name' is a parameter
    print(f"Hello, {name}")

greet("Ada")  # "Ada" is an argument
```

### Multiple Parameters
Functions can have multiple parameters separated by commas.

### Code Example:
```python
def introduce(name, age):
    print(f"{name} is {age} years old")

introduce("Grace", 85)  # First argument goes to name, second to age
```

## Positional vs Keyword Arguments

### Positional Arguments
Arguments are matched based on their position in the function call.

### Code Example:
```python
def introduce(name, age):
    print(f"{name} is {age} years old")

introduce("Ada", 36)  # Ada goes to name, 36 goes to age
introduce(36, "Ada")  # This would be semantically wrong - 36 goes to name, "Ada" goes to age
```

### Keyword Arguments
You can explicitly specify which parameter each value belongs to using parameter names.

### Code Example:
```python
def introduce(name, age):
    print(f"{name} is {age} years old")

introduce(age=36, name="Ada")  # Order doesn't matter with keyword arguments
```

## Default Parameters

Parameters can have default values. If no argument is provided for a parameter with a default value, the default value is used.

### Code Example:
```python
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}")

greet("Ada")  # Uses default greeting: "Hello, Ada"
greet("Ada", "Good morning")  # Uses provided greeting: "Good morning, Ada"
```

### Important Rule:
Required parameters must come before parameters with default values.

### Code Example:
```python
# This is correct
def greet(greeting="Hello", name):
    print(f"{greeting}, {name}")

# This will cause an error
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}")
```

## Return Statement

The `return` statement sends a value back to where the function was called. Functions that don't explicitly return a value return `None`.

### Code Example:
```python
def calculate_tax(income, tax_rate):
    tax = income * tax_rate
    return tax  # Returns the calculated tax value

result = calculate_tax(50000, 0.3)
print(result)  # Prints 15000.0
```

### Difference between Print and Return:
```python
def add_with_print(a, b):
    print(a + b)  # Only prints the result
    # No return statement - implicitly returns None

def add_with_return(a, b):
    return a + b  # Returns the value for use elsewhere

result1 = add_with_print(5, 3)  # Prints 8 but result1 is None
result2 = add_with_return(5, 3)  # Returns 8 and result2 is 8
```

## Return Statement Behavior

The `return` statement immediately ends the function execution. Any code after a return in the same function execution will not run.

### Code Example:
```python
def example():
    print("Before return")
    return 10
    print("After return")  # This line will never execute

result = example()  # Prints "Before return" and returns 10
print(result)  # Prints 10
```

## Function with Conditional Logic

Functions can use conditional statements, and `return` works naturally with them.

### Code Example:
```python
def check_grade(score):
    if score >= 70:
        return "Pass"
    else:
        return "Fail"

print(check_grade(85))  # Prints "Pass"
print(check_grade(60))  # Prints "Fail"
```

## Functions with Collections

Functions can accept and work with collections like lists and dictionaries.

### Code Example with Lists:
```python
def get_first_item(items):
    return items[0]

languages = ["Python", "Java", "C#"]
first_language = get_first_item(languages)
print(first_language)  # Prints "Python"
```

### Code Example with Loops in Functions:
```python
def print_languages(languages):
    for language in languages:
        print(language)

languages = ["Python", "Java", "C#"]
print_languages(languages)
```

### Code Example with List Processing:
```python
def count_passing_scores(scores):
    passing = 0
    for score in scores:
        if score >= 70:
            passing += 1
    return passing

scores = [85, 62, 91, 70, 48]
result = count_passing_scores(scores)
print(f"{result} students passed")  # Prints "3 students passed"
```

### Code Example with Dictionaries:
```python
def get_student_status(student):
    if student["score"] >= 70:
        return "Pass"
    else:
        return "Fail"

student = {"name": "Ada", "score": 85}
status = get_student_status(student)
print(f"{student['name']} {status}")  # Prints "Ada Pass"
```

### Code Example with List of Dictionaries:
```python
def get_student_status(student):
    if student["score"] >= 70:
        return "Pass"
    else:
        return "Fail"

students = [
    {"name": "Ada", "score": 85},
    {"name": "Bob", "score": 62},
    {"name": "Charlie", "score": 91}
]

for student in students:
    status = get_student_status(student)
    print(f"{student['name']} {status}")
```

## Function Composition

Functions can call other functions, enabling code reuse and separation of concerns.

### Code Example:
```python
def calculate_tax(income, tax_rate):
    return income * tax_rate

def calculate_income_after_tax(income, tax_rate):
    tax = calculate_tax(income, tax_rate)
    return income - tax

result = calculate_income_after_tax(50000, 0.3)
print(result)  # Prints 35000.0
```

## Type Hints

Type hints are optional annotations that indicate what data types you expect for parameters and return values. They don't affect runtime behavior but improve code readability.

### Code Example:
```python
def add(a: int, b: int) -> int:
    return a + b

result = add(5, 3)  # Type hints show we expect integers
print(result)  # Prints 8
```

### Important Note:
Type hints are not enforced at runtime. Python still runs with dynamic typing.

### Code Example:
```python
def add(a: int, b: int) -> int:
    return a + b

# This will run but produce unexpected result
result = add("hello", "world")  # Returns "helloworld" - no error!
```

## Docstrings

Docstrings are string literals that appear right after the function definition. They describe what the function does and help with documentation.

### Code Example:
```python
def calculate_area(width: float, height: float) -> float:
    """
    Calculate the area of a rectangle.
    
    Args:
        width (float): The width of the rectangle
        height (float): The height of the rectangle
    
    Returns:
        float: The area of the rectangle
    """
    return width * height

# Accessing docstring
print(calculate_area.__doc__)
```

## Common Mistakes to Avoid

1. **Forgetting parentheses when calling functions**
```python
def greet():
    print("Hello")

greet()  # Correct - includes parentheses
greet    # Incorrect - doesn't call the function
```

2. **Confusing print and return**
```python
# Wrong approach - prints but returns None
def add_wrong(a, b):
    print(a + b)  # Only prints

# Correct approach - returns value for use
def add_correct(a, b):
    return a + b  # Returns the value
```

3. **Incorrect argument count**
```python
def introduce(name, age):
    print(f"{name} is {age} years old")

introduce("Ada")  # Error - missing required argument 'age'
```

4. **Wrong parameter order with positional arguments**
```python
def introduce(name, age):
    print(f"{name} is {age} years old")

introduce(36, "Ada")  # Semantically wrong - 36 becomes name, "Ada" becomes age
```

## Key Takeaways

1. Functions are reusable blocks of code that perform specific tasks
2. Always define functions before calling them (Python reads from top to bottom)
3. Use `def` keyword to create functions
4. Functions must be called with parentheses to execute
5. Parameters allow functions to accept input values
6. Default parameters provide fallback values when arguments aren't provided
7. Return statements send values back to the caller and end function execution
8. Type hints improve code readability but aren't enforced at runtime
9. Docstrings help document what functions do
10. Functions can call other functions, enabling code reuse and better organization