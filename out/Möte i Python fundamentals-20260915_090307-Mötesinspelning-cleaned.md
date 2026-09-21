## Introduction to Python Functions and Scope

### Recap of Basic Function Syntax
Functions in Python are defined using the `def` keyword followed by the function name and parameters in parentheses. The function body is indented and includes code to be executed when the function is called. For example:
```python
def calculate_total(price, quantity):
    return price * quantity
```
When calling the function, the arguments passed are assigned to the parameters in the order specified. For instance:
```python
result = calculate_total(199, 3)
print(result)
```
The `return` statement in a function returns a calculated value back to the caller, allowing the caller to use or store the result. Unlike `print`, which outputs to the console, `return` sends the value back to the calling code.

### Understanding Variable Scope
#### Local Scope
Variables declared inside a function are local to that function and cannot be accessed outside of it. For example:
```python
def greet():
    message = "Hello from the function"
    print(message)

greet()
print(message)  # Raises a NameError
```
#### Global Scope
Variables defined outside any function have a global scope and can be accessed from anywhere in the code. However, directly modifying a global variable inside a function can be tricky and is generally discouraged for clarity and maintainability:
```python
message = "Hello from global"
def greet():
    global message
    message = "Local message"
    print(message)

greet()
print(message)  # Prints "Local message"
```
Using the `global` keyword within a function explicitly tells Python to modify the global variable instead of creating a new local variable with the same name.

### Scope Lookup Rule (LEGB)
Python follows the LEGB rule for scope lookup:
- **Local (L)**: Inside the function.
- **Enclosing (E)**: Inside any enclosing functions (nested functions).
- **Global (G)**: At the top level of the file.
- **Built-in (B)**: In the built-in Python namespace.

Python searches for names starting from the local scope and moving outward until it finds a match. For example:
```python
def outer():
    message = "Hello from outer"
    def inner():
        print(message)  # Accesses the message from the outer function
    inner()

outer()
```

### Using `args` for Flexible Function Parameters
The `*args` syntax allows functions to accept a variable number of positional arguments:
```python
def show_numbers(*numbers):
    print(numbers)

show_numbers(1, 2, 3)
```
`args` collects all positional arguments into a tuple, enabling functions to handle an arbitrary number of inputs.

### Using `kwargs` for Keyword Arguments
The `**kwargs` syntax allows functions to accept a variable number of keyword arguments:
```python
def show_user(**user_info):
    print(user_info)

show_user(name="Ada", age=36)
```
`kwargs` collects all keyword arguments into a dictionary, making it flexible to handle varying sets of named parameters.

### Practical Examples and Exercises
Understanding how to use `args` and `kwargs` effectively can greatly enhance the flexibility of your functions. However, always consider clarity and maintainability when choosing between fixed and flexible parameter lists.