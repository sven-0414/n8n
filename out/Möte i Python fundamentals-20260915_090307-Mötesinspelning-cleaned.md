## Python Functions and Scope

### Recap of Functions
Functions are a way to encapsulate code that performs a specific task, making the code reusable and easier to manage. A function can be defined using the `def` keyword, followed by the function name and its parameters in parentheses. The function body is indented and contains the operations to be performed.

#### Example
```python
def calculate_total(price, quantity):
    return price * quantity

result = calculate_total(293, 3)
print(result)
```

### Parameters vs Arguments
- **Parameters**: Variables that act as placeholders for the values that are passed to the function when it is called.
- **Arguments**: The actual values that are passed to the function.

#### Example
```python
def greet(message):
    print(message)

greet("Hello, world!")
```
- Here, `message` is the parameter and `"Hello, world!"` is the argument.

### Understanding Scope
Scope refers to the part of the code where a variable can be accessed. Variables defined inside a function are called local variables and are only accessible within that function.

#### Example
```python
def local_variable_example():
    message = "Local message"
    print(message)

local_variable_example()
# print(message)  # This will raise a NameError: name 'message' is not defined
```

### Local and Global Variables
- **Local Variables**: Variables defined inside a function are local and can only be accessed within that function.
- **Global Variables**: Variables defined outside any function can be accessed globally throughout the script, unless they are redefined inside a function.

#### Example
```python
message = "Global message"

def print_message():
    message = "Local message"
    print(message)

print_message()
print(message)  # Prints "Global message"
```

### Changing Global Variables
The `global` keyword is used to inform Python that a variable is a global variable, even when it is used inside a function.

#### Example
```python
x = 10

def modify_global():
    global x
    x = 20
    print(x)

modify_global()
print(x)  # Prints 20
```

### Nested Functions and Enclosing Scope
Enclosing scope refers to the scope that is enclosed by another function. If a variable is defined in the enclosing scope, it can be accessed by nested functions.

#### Example
```python
def outer_function():
    message = "Outer message"

    def inner_function():
        print(message)

    inner_function()

outer_function()
```

### Using `*args` and `**kwargs`
- `*args` is used to pass a variable number of non-keyworded arguments to a function.
- `**kwargs` is used to pass a variable number of keyworded arguments to a function.

#### Example
```python
def show_numbers(*args):
    for number in args:
        print(number)

show_numbers(1, 2, 3, 4)

def show_user(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

show_user(name="Ada", age=36, city="London")
```

### Unpacking Arguments
Unpacking is used to unpack the elements of a list or tuple into positional arguments of a function, or the elements of a dictionary into keyword arguments.

#### Example
```python
def add_numbers(a, b, c):
    return a + b + c

numbers = [10, 20, 30]
print(add_numbers(*numbers))  # Unpacks the list

user = {"name": "Ada", "age": 36, "city": "London"}
print(show_user(**user))  # Unpacks the dictionary
```

### Summary
Understanding scope, local and global variables, and using `*args` and `**kwargs` makes your Python functions more flexible and maintainable. These concepts are crucial for writing clean and efficient Python code.