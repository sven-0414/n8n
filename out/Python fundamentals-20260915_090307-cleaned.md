# Python Functions and Scope

## Function Basics

### Parameters vs Arguments
**Definition**: Parameters are the variables defined in the function signature, while arguments are the actual values passed when calling the function.

**Code Example**:
```python
def calculate_total(price, quantity):
    return price * quantity

result = calculate_total(199, 3)  # 199 and 3 are arguments
```

**Key Points**:
- Parameters: `price` and `quantity` in the function definition
- Arguments: `199` and `3` when calling the function
- Order matters when passing positional arguments

### Return Statement
**Definition**: The `return` statement sends a value back to the caller and exits the function immediately.

**Code Example**:
```python
def multiply(a, b):
    return a * b

result = multiply(5, 3)  # result equals 15
```

## Variable Scope

### Local vs Global Scope
**Definition**: Local variables exist only within the function where they're defined, while global variables exist throughout the entire program.

**Code Example**:
```python
message = "Hello from global scope"

def greet():
    message = "Hello from local scope"  # This is a local variable
    print(message)

greet()  # Prints: Hello from local scope
print(message)  # Prints: Hello from global scope
```

### The L.E.G.B. Rule
**Definition**: Python's lookup order for variable names follows the L.E.G.B. rule:
- **L**ocal: Inside current function
- **E**nclosing: In enclosing functions
- **G**lobal: At module level
- **B**uilt-in: In built-in namespace

**Code Example**:
```python
name = "global"

def outer_function():
    name = "enclosing"
    
    def inner_function():
        name = "local"
        print(name)  # Prints: local (from Local scope)
    
    inner_function()

outer_function()
```

### Global Keyword
**Definition**: The `global` keyword allows a function to modify variables in the global scope.

**Code Example**:
```python
counter = 0

def increase_counter():
    global counter
    counter += 1

increase_counter()
print(counter)  # Prints: 1
```

## Function Parameters and Arguments

### Variable Length Arguments (*args)
**Definition**: The `*args` syntax allows a function to accept any number of positional arguments, which are packed into a tuple.

**Code Example**:
```python
def show_numbers(*args):
    print(args)  # args is a tuple
    for num in args:
        print(num)

show_numbers(1, 2, 3, 4)  # Prints each number on separate lines
```

### Keyword Arguments (**kwargs)
**Definition**: The `**kwargs` syntax allows a function to accept any number of keyword arguments, which are packed into a dictionary.

**Code Example**:
```python
def show_user(**kwargs):
    print(kwargs)  # kwargs is a dictionary
    for key, value in kwargs.items():
        print(f"{key}: {value}")

show_user(name="Ada", age=36, city="London")
```

### Combining Parameters
**Definition**: Functions can use regular parameters, *args, and **kwargs together in any order.

**Code Example**:
```python
def create_user(username, *hobbies, **user_info):
    print(f"Username: {username}")
    print(f"Hobbies: {hobbies}")
    print(f"Info: {user_info}")

create_user("ada123", "reading", "swimming", age=36, city="London")
```

## Function Best Practices

### Avoiding Global Variables
**Definition**: Using global variables makes code harder to test and maintain. Returning values is generally cleaner.

**Code Example**:
```python
# Less preferred - using global
counter = 0

def increase_counter():
    global counter
    counter += 1
    return counter

# Better approach - returning values
def increase_counter(current_count):
    return current_count + 1

counter = 0
counter = increase_counter(counter)
```

### Unpacking Arguments
**Definition**: The `*` and `**` operators can unpack collections into separate arguments.

**Code Example**:
```python
def introduce(name, age, city):
    print(f"Name: {name}, Age: {age}, City: {city}")

# Unpacking list with *
numbers = [10, 20, 30]
add_numbers(*numbers)  # Same as add_numbers(10, 20, 30)

# Unpacking dictionary with **
person = {"name": "Ada", "age": 36, "city": "London"}
introduce(**person)  # Same as introduce(name="Ada", age=36, city="London")
```

## Common Pitfalls and Edge Cases

### Unbound Local Error
**Definition**: When Python sees an assignment to a variable inside a function, it treats that variable as local for the entire function, even if it's referenced before assignment.

**Code Example**:
```python
total = 100

def add_tax():
    print(total)  # This will cause UnboundLocalError
    total = total * 1.25  # Assignment makes total local
    return total
```

**Solution**: Use parameters or return values instead of global variables.

### Scope Confusion with Built-ins
**Definition**: Overwriting built-in names like `list`, `str`, `print` can cause unexpected behavior.

**Code Example**:
```python
# Bad practice - overwrites built-in
list = [1, 2, 3]  # This overwrites the built-in list function

# Good practice - avoid built-in names
my_list = [1, 2, 3]
```

### Parameter vs Arguments Order
**Definition**: When using both regular parameters and *args, parameters must come before *args.

**Code Example**:
```python
def process_data(required_param, *args):
    print(f"Required: {required_param}")
    print(f"Args: {args}")

process_data("first", 1, 2, 3)  # Correct order
```

## Why These Concepts Matter

Understanding scope is crucial for writing maintainable code because it prevents unintended side effects and makes debugging easier. Functions with clear local scopes don't interfere with each other's variables.

The flexibility of *args and **kwargs allows you to write more adaptable functions that can handle varying numbers of inputs, making your code more reusable and robust. However, these features should be used judiciously - they shouldn't replace clear, intentional function signatures when the number of parameters is known and fixed.

The L.E.G.B. lookup rule explains Python's behavior when resolving variable names, which is essential for understanding why certain variables are accessible in specific contexts and how to properly manage your code's namespace hierarchy.