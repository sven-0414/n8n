# Python Fundamentals

## Overview and Introduction

Python was created by Guido van Rossum and first released in the early 1990s. The main idea behind Python was to reduce unnecessary syntax and make code relatively easy to read, which is why it became popular as a beginner language. Python is also widely used in advanced sectors such as automation, data science, artificial intelligence, and machine learning.

Python's design philosophy emphasizes readability and simplicity, making it an excellent choice for both beginners and experienced developers. Its popularity in AI and data science stems from the fact that many mathematicians and researchers who develop these algorithms have a mathematical background but not necessarily a programming background, so they needed a language that was easy to learn.

## Python Interpreter

The Python interpreter allows you to execute code directly without creating a file first. You can start it by typing `python` in your terminal. Here's an example of basic usage:

```python
>>> print("Hello World")
Hello World
>>> 5 + 2
7
>>> 5 / 2
2.5
```

To exit the interpreter, you can use `exit()` or `Ctrl+D` (Linux/Mac) or `Ctrl+Z` followed by Enter (Windows).

## Data Types

Python has several built-in data types:

### Basic Data Types

1. **Integer (int)**: Whole numbers without decimal points
2. **Float**: Numbers with decimal points
3. **String (str)**: Sequences of characters enclosed in quotes
4. **Boolean (bool)**: True or False values

```python
# Examples of different data types
age = 25              # int
price = 19.99         # float  
name = "John"         # str
is_student = True     # bool
```

**Important Note**: In Python, boolean values must start with capital letters: `True` and `False`. Lowercase `true` and `false` will not work.

## Variables and Variable Naming

### Dynamic Typing

Python is dynamically typed, meaning you don't need to explicitly declare the data type of a variable. The type is determined automatically from the value assigned to it.

```python
number = 10        # int
number = 10.5      # float  
number = "hello"   # str
```

This behavior allows variables to change types during execution, which is different from statically typed languages like Java or C# where you must declare the type upfront.

### Variable Naming Rules

1. Variables cannot start with a number (e.g., `5name` is invalid)
2. Variables cannot contain spaces (e.g., `first name` is invalid)
3. Variables cannot contain special characters except underscores
4. Python is case-sensitive (`Name` and `name` are different variables)

```python
# Valid variable names
first_name = "John"
_age = 25
user123 = "Alice"

# Invalid variable names  
# 5name = "John"     # Invalid - starts with number
# first name = "John"  # Invalid - contains space
# first-name = "John"  # Invalid - contains special character
```

## Arithmetic Operators

Python supports standard arithmetic operations:

```python
print(5 + 2)      # Addition: 7
print(5 - 2)      # Subtraction: 3  
print(5 * 2)      # Multiplication: 10
print(5 / 2)      # Division: 2.5 (floating point result)
print(5 ** 2)     # Exponentiation: 25 (5 to the power of 2)
print(5 // 2)     # Floor division: 2 (removes decimal part)
print(5 % 2)      # Modulus: 1 (remainder after division)
```

### Floor Division and Modulus

Floor division (`//`) removes the decimal part without rounding:
```python
print(7 // 3)    # Result: 2
```

Modulus (`%`) gives the remainder of a division:
```python
print(7 % 3)     # Result: 1
```

These operators are particularly useful for determining if numbers are even or odd:
```python
number = 5
if number % 2 == 0:
    print("Even")
else:
    print("Odd")  # This will print since 5 is odd
```

## Type Conversion

Since Python is strongly typed, you sometimes need to explicitly convert values:

```python
# Converting string to integer
number_as_text = "10"
number = int(number_as_text) + 5  # Result: 15

# Converting integer to string  
value = 10
value_as_text = str(value) + " apples"  # Result: "10 apples"

# Converting to float
number = float("3.14")  # Result: 3.14

# Converting to boolean
print(bool(1))    # Result: True
print(bool(0))    # Result: False
```

### Important Notes About Type Conversion

1. **String to Number**: You must convert strings that represent numbers to integers or floats before doing mathematical operations
2. **Number to String**: When concatenating with strings, you need to convert numbers to strings
3. **Boolean Conversion**: In Python, `0`, `False`, empty strings, and empty containers evaluate to `False` when converted to boolean

## Strings

### String Basics

Strings in Python are sequences of characters. You can use either single quotes or double quotes:

```python
text1 = 'Hello'
text2 = "Hello"
print(text1 == text2)  # Result: True (both are the same)

# Using apostrophes in strings
message = "I'm learning Python"  # Double quotes work better with apostrophes
```

### String Concatenation

You can join strings using the `+` operator:

```python
first_name = "John"
last_name = "Doe"
full_name = first_name + " " + last_name
print(full_name)  # Result: John Doe
```

**Important**: You cannot directly concatenate strings with numbers. This will cause a TypeError:

```python
age = 30
# print("I am " + age + " years old")  # This will cause TypeError
print("I am " + str(age) + " years old")  # Correct way: convert to string first
```

### F-strings (Formatted String Literals)

Python provides a more elegant way to include variables in strings using f-strings:

```python
name = "Alice"
age = 25
print(f"{name} is {age} years old")  # Result: Alice is 25 years old
```

F-strings are cleaner than manual conversion and work with different data types without explicit conversion.

### String Length and Indexing

```python
language = "Python"
print(len(language))  # Result: 6 (number of characters)

# Indexing (0-based)
print(language[0])    # Result: P (first character)
print(language[-1])   # Result: n (last character)
print(language[-2])   # Result: o (second to last character)
```

### String Slicing

Slicing allows you to extract portions of strings:

```python
text = "Python"
print(text[0:3])      # Result: Pyt (from index 0 to 2, not including 3)
print(text[2:])       # Result: thon (from index 2 to end)
print(text[:3])       # Result: Pyt (from beginning to index 2)
print(text[::2])      # Result: Pto (every other character)
print(text[::-1])     # Result: nohtyP (reversed string)
```

**Important**: In slicing, the end index is not included in the result.

## String Immutability

Strings in Python are immutable, meaning you cannot change individual characters after creating a string:

```python
word = "Python"
# word[0] = "J"  # This will cause TypeError: 'str' object does not support item assignment

# However, you can create a new string
new_word = "J" + word[1:]  # Result: "Jython"
print(new_word)            # Result: Jython
```

## String Methods

Strings come with many built-in methods for manipulation:

```python
message = "  Hello Python  "

# Lowercase conversion
print(message.lower())    # Result: "  hello python  "

# Uppercase conversion  
print(message.upper())    # Result: "  HELLO PYTHON  "

# Remove whitespace from both ends
print(message.strip())    # Result: "Hello Python"

# Replace text
print(message.replace("Python", "World"))  # Result: "  Hello World  "

# Split string into list
data = "apple,banana,orange"
fruits = data.split(",")  # Result: ['apple', 'banana', 'orange']
```

**Important**: String methods return new strings rather than modifying the original string. The original string remains unchanged.

## Summary

Python's design emphasizes readability and simplicity, making it an excellent choice for beginners while still being powerful enough for advanced applications like data science and AI. Key concepts covered include:

1. Dynamic typing - variables can change types during execution
2. Simple syntax - no need to declare variable types explicitly
3. Built-in data types (int, float, str, bool)
4. Arithmetic operations with special operators (floor division, modulus)
5. Type conversion for working with different data types
6. String manipulation through indexing, slicing, and methods
7. String immutability - strings cannot be modified after creation

These fundamentals form the foundation for more advanced Python concepts that will be covered in upcoming lessons.