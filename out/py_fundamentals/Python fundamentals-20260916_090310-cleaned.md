# Python Fundamentals: Comprehensions, Enumerate, Zip, Unpacking, and Lambda

## List Comprehensions

### Definition and Basic Syntax
List comprehensions are a compact way to create lists by applying transformations or filtering existing collections. They follow the pattern: `[expression for item in iterable if condition]`.

### Code Example
```python
# Traditional approach
numbers = [1, 2, 3, 5]
doubled_numbers = []
for number in numbers:
    doubled_numbers.append(number * 2)
print(doubled_numbers)  # Output: [2, 4, 6, 10]

# List comprehension approach
numbers = [1, 2, 3, 5]
doubled_numbers = [number * 2 for number in numbers]
print(doubled_numbers)  # Output: [2, 4, 6, 10]
```

### Key Points
- The basic structure is `[expression for item in iterable]`
- You can add filtering with `if condition` at the end
- The square brackets `[]` create a list comprehension
- List comprehensions are more concise and often faster than traditional loops

### Why It Matters
List comprehensions are considered Pythonic because they make code more readable and expressive. They're extremely common in Python codebases and represent idiomatic Python programming.

## Dictionary Comprehensions

### Definition and Basic Syntax
Dictionary comprehensions follow the same pattern as list comprehensions but use curly braces `{}` and require both key and value expressions: `{key_expression: value_expression for item in iterable if condition}`.

### Code Example
```python
# Traditional approach
numbers = [1, 2, 3, 4, 5]
squares_dict = {}
for number in numbers:
    squares_dict[number] = number ** 2
print(squares_dict)  # Output: {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}

# Dictionary comprehension approach
numbers = [1, 2, 3, 4, 5]
squares_dict = {number: number ** 2 for number in numbers}
print(squares_dict)  # Output: {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}
```

### Filtering with Dictionary Comprehensions
```python
scores = {"Anna": 85, "Bob": 62, "Charlie": 91, "Diana": 70}
passed_students = {name: score for name, score in scores.items() if score >= 70}
print(passed_students)  # Output: {'Anna': 85, 'Charlie': 91, 'Diana': 70}
```

### Why It Matters
Dictionary comprehensions make it easy to transform and filter dictionaries in a single, readable line of code. They're particularly useful when creating new dictionaries based on existing data.

## Set Comprehensions

### Definition and Basic Syntax
Set comprehensions use curly braces `{}` without key-value pairs: `{expression for item in iterable if condition}`.

### Code Example
```python
words = ["Python", "Java", "Python", "C#", "Java"]
lengths = {len(word) for word in words}
print(lengths)  # Output: {5, 6, 2} (order may vary)
```

### Why It Matters
Set comprehensions are useful for creating sets from existing collections while automatically handling duplicates. They're particularly helpful when you need unique values from a collection.

## Generator Expressions

### Definition and Basic Syntax
Generator expressions look like list comprehensions but use parentheses `()` instead of square brackets `[]`. They create generator objects that produce items on-demand rather than storing them all in memory.

### Code Example
```python
# List comprehension (creates list)
numbers = [number * 2 for number in range(5)]
print(numbers)  # Output: [0, 2, 4, 6, 8]

# Generator expression (creates generator object)
numbers = (number * 2 for number in range(5))
print(numbers)  # Output: <generator object at 0x...>
print(list(numbers))  # Output: [0, 2, 4, 6, 8] - convert to list to see values
```

### Why It Matters
Generator expressions are memory-efficient because they generate values one at a time rather than storing all values in memory. They're particularly useful when working with large datasets.

## Enumerate Function

### Definition and Basic Syntax
The `enumerate()` function adds a counter to an iterable and returns it as an enumerate object. The syntax is `enumerate(iterable, start=0)`.

### Code Example
```python
languages = ["Python", "Java", "C#"]
for index, language in enumerate(languages):
    print(f"{index}: {language}")
# Output:
# 0: Python
# 1: Java
# 2: C#

# With custom starting number
for position, language in enumerate(languages, start=1):
    print(f"{position}: {language}")
# Output:
# 1: Python
# 2: Java
# 3: C#
```

### Why It Matters
`enumerate()` eliminates the need to manually manage index counters, making code cleaner and less error-prone. It's commonly used when both the index and value are needed.

## Zip Function

### Definition and Basic Syntax
The `zip()` function combines multiple iterables element-wise into tuples. The syntax is `zip(iterable1, iterable2, ...)`.

### Code Example
```python
names = ["Anna", "Bob", "Charlie"]
scores = [85, 62, 91]
for name, score in zip(names, scores):
    print(f"{name}: {score}")
# Output:
# Anna: 85
# Bob: 62
# Charlie: 91

# With three lists
names = ["Anna", "Bob", "Charlie"]
scores = [85, 62, 91]
cities = ["Stockholm", "Göteborg", "Malma"]
for name, score, city in zip(names, scores, cities):
    print(f"{name}: {score}, {city}")
# Output:
# Anna: 85, Stockholm
# Bob: 62, Göteborg
# Charlie: 91, Malma
```

### Handling Different Length Lists
```python
names = ["Anna", "Bob", "Charlie", "Diana"]
scores = [85, 62, 91]  # Shorter list
for name, score in zip(names, scores):
    print(f"{name}: {score}")
# Output:
# Anna: 85
# Bob: 62
# Charlie: 91
# Diana is skipped because zip stops at the shortest iterable
```

### Creating Dictionaries with Zip
```python
names = ["Anna", "Bob", "Charlie"]
scores = [85, 62, 91]
student_scores = dict(zip(names, scores))
print(student_scores)  # Output: {'Anna': 85, 'Bob': 62, 'Charlie': 91}
```

### Why It Matters
`zip()` is essential for pairing elements from multiple collections simultaneously. It's particularly useful when you need to process corresponding elements from different data sources.

## Unpacking

### Definition and Basic Syntax
Unpacking allows assigning elements from iterables (lists, tuples) to individual variables using the assignment operator `=`.

### Code Example
```python
# Basic unpacking
coordinates = [10, 20]
x, y = coordinates
print(x, y)  # Output: 10 20

# Unpacking with lists
names = ["Ada", "Grace", "Vito"]
first, second, third = names
print(first, second, third)  # Output: Ada Grace Vito

# Packing remaining elements
numbers = [10, 20, 30, 40, 50]
first, *rest = numbers
print(first, rest)  # Output: 10 [20, 30, 40, 50]

# Unpacking with multiple variables
first, middle, *last = numbers
print(first, middle, last)  # Output: 10 20 [30, 40, 50]
```

### Ignoring Values with Underscore
```python
person = ["Ada", 36, "London"]
name, _, city = person
print(name, city)  # Output: Ada London
```

### Combining Collections with Asterisks
```python
first_list = [1, 2, 3]
second_list = [4, 5, 6]
combined = [*first_list, *second_list]
print(combined)  # Output: [1, 2, 3, 4, 5, 6]

# Dictionary merging with double asterisks
defaults = {"theme": "light", "language": "English"}
user_settings = {"language": "Swedish", "notification": "on"}
settings = {**defaults, **user_settings}
print(settings)  # Output: {'theme': 'light', 'language': 'Swedish', 'notification': 'on'}
```

### Why It Matters
Unpacking makes code more readable and concise when working with collections. It's particularly useful for handling function arguments, processing data structures, and combining multiple sequences.

## Lambda Functions

### Definition and Basic Syntax
Lambda functions are small anonymous functions defined with the `lambda` keyword. The syntax is `lambda parameters: expression`.

### Code Example
```python
# Traditional function
def double(number):
    return number * 2

print(double(5))  # Output: 10

# Lambda function equivalent
double = lambda number: number * 2
print(double(5))  # Output: 10
```

### Using Lambda with Sorted
```python
names = ["Alexander", "Lee", "Grace", "Ada"]
# Sort by length using lambda
sorted_by_length = sorted(names, key=lambda name: len(name))
print(sorted_by_length)  # Output: ['Lee', 'Ada', 'Grace', 'Alexander']
```

### Using Lambda with Map
```python
numbers = [1, 2, 3, 4, 5]
doubled = list(map(lambda number: number * 2, numbers))
print(doubled)  # Output: [2, 4, 6, 8, 10]
```

### Using Lambda with Filter
```python
numbers = [1, 2, 3, 4, 5, 6]
even_numbers = list(filter(lambda number: number % 2 == 0, numbers))
print(even_numbers)  # Output: [2, 4, 6]
```

### Why It Matters
Lambda functions are useful for creating small, one-time functions that don't need to be reused. They're commonly used as arguments to higher-order functions like `map()`, `filter()`, and `sorted()`.

## Best Practices

### When to Use List Comprehensions vs Loops
- **Use list comprehensions** when the transformation is simple and readable
- **Use traditional loops** when the logic is complex or readability suffers with comprehension

### Readability Guidelines
```python
# Good: Simple transformation
doubled = [x * 2 for x in numbers]

# Avoid: Complex logic in comprehension
complex_result = [process(x) for x in data if condition1(x) and condition2(x) and condition3(x)]

# Better: Use traditional loop for complex logic
result = []
for x in data:
    if condition1(x) and condition2(x) and condition3(x):
        result.append(process(x))
```

### Memory Considerations
- **Use generators** when working with large datasets to avoid memory issues
- **Use list comprehensions** when you need all values at once
- **Use traditional loops** when processing data one item at a time

## Key Takeaways

1. **List comprehensions** provide a concise way to create lists by applying transformations or filtering existing collections
2. **Dictionary comprehensions** work similarly but create dictionaries with key-value pairs
3. **Set comprehensions** create sets from iterables, automatically handling duplicates
4. **Generator expressions** are memory-efficient for large datasets
5. **Enumerate** adds counters to iterables without manual index management
6. **Zip** combines multiple iterables element-wise into tuples
7. **Unpacking** assigns elements from collections to individual variables
8. **Lambda functions** create small anonymous functions useful with higher-order functions

These concepts form the foundation of Pythonic programming patterns and are essential for writing clean, readable, and efficient Python code.