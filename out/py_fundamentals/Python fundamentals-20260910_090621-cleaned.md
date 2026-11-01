# Python Control Flow and Loops

## Comparisons and Boolean Values

### Understanding Comparison Expressions
A comparison expression in Python evaluates to a Boolean value - either `True` or `False`. These expressions are fundamental for making decisions in your code.

```python
# Examples of comparisons
print(5 > 2)      # True
print(5 < 2)      # False
print(5 == 2)     # False
print(5 != 2)     # True
print(5 >= 2)     # True
print(5 <= 2)     # False
```

**Key Points:**
- Single equals (`=`) is for assignment, double equals (`==`) is for comparison
- Python uses `==` for equality comparison, not `===` like JavaScript
- Comparison operators return Boolean values (True/False)

### Important Distinction: Assignment vs Comparison
```python
number = 10      # Assignment - stores 10 in variable number
print(number == 10)  # Comparison - checks if number equals 10, returns True
```

**Gotchas:**
- Using `=` instead of `==` in conditions will cause errors
- Python distinguishes between integer and string types in comparisons
- `1 == "1"` evaluates to `False` in Python (unlike JavaScript's loose equality)

### Type Comparison Behavior
```python
print(1 == 1)     # True - same type
print(1 == "1")   # False - different types (integer vs string)
print(1 is 1)     # True - same object identity
```

## Logical Operators

### The `and` Operator
The `and` operator requires **both** conditions to be `True` for the entire expression to evaluate to `True`.

```python
age = 25
has_ticket = True

print(age >= 18 and has_ticket)  # True - both conditions are true
```

### The `or` Operator
The `or` operator requires **at least one** condition to be `True` for the entire expression to evaluate to `True`.

```python
is_admin = False
is_teacher = True

print(is_admin or is_teacher)  # True - at least one condition is true
```

### The `not` Operator
The `not` operator reverses the Boolean value of an expression.

```python
is_logged_in = False
print(not is_logged_in)  # True - reverses false to true
```

### Using `in` and `not in` as Conditions
The `in` operator returns a Boolean value, which can be used directly in conditional statements.

```python
languages = ["Python", "Java", "C Sharp"]

print("Python" in languages)    # True
print("Rust" in languages)       # False
print("Rust" not in languages)  # True
```

**Why this matters:** This allows for clean, readable code when checking membership in collections.

## Conditional Statements

### The `if` Statement Structure
Python uses indentation to define code blocks instead of curly braces like other languages.

```python
age = 20

if age >= 18:
    print("adult")
```

**Key Rules:**
- Always end the condition with a colon (`:`)
- Use consistent indentation (typically 4 spaces)
- Code that belongs to the if block must be indented
- Python is sensitive to indentation - incorrect indentation will cause errors

### The `if-else` Statement
```python
age = 16

if age >= 18:
    print("adult")
else:
    print("under 18")
```

### The `if-elif-else` Statement
```python
score = 80

if score >= 90:
    print("Grade A")
elif score >= 80:
    print("Grade B")
elif score >= 70:
    print("Grade C")
else:
    print("Below C")
```

**Important Point:** Python evaluates conditions from top to bottom. As soon as one condition is `True`, that block executes and the rest are skipped.

### Order Matters in Conditional Logic
```python
score = 95

# Wrong order - will always print "Grade C or higher"
if score <= 70:
    print("Grade C or higher")
elif score >= 90:
    print("Grade A")
else:
    print("Grade B")

# Correct order - will properly print "Grade A"
if score >= 90:
    print("Grade A")
elif score >= 80:
    print("Grade B")
elif score <= 70:
    print("Grade C or higher")
```

### Nested Conditional Statements
```python
age = 16
has_ticket = True

if age >= 18:
    if has_ticket:
        print("You may enter")
    else:
        print("Entry denied - no ticket")
else:
    print("Entry denied - you are too young")
```

**Best Practice:** Avoid deep nesting. Use combined conditions when possible for cleaner, more readable code.

## Truthy and Falsy Values

### Understanding Truthy/Falsy
Python treats certain values as truthy or falsy without explicit Boolean conversion.

```python
name = ""
if name:
    print("Name exists")
else:
    print("The name is empty")

# Output: The name is empty
```

**Falsy values in Python include:**
- Empty strings (`""`)
- Empty lists (`[]`)
- Zero (`0`)
- `None`
- `False`

### Practical Example with Falsy Values
```python
items = []
if items:
    print("The list contains data")
else:
    print("The list is empty")

# Output: The list is empty
```

**Note:** Even a string with just spaces is truthy, unlike empty strings.

## Looping Constructs

### For Loops - Iterating Over Collections

#### Basic For Loop
```python
languages = ["Python", "Java", "C Sharp"]

for language in languages:
    print(language)
# Output: Python, Java, C Sharp
```

**Key Concepts:**
- Python's `for` loop iterates directly over elements, not using counters like traditional for loops
- The variable (`language`) represents the current element during each iteration
- No need to manage counter variables manually

#### Looping Through Strings
```python
word = "Python"

for character in word:
    print(character)
# Output: P, y, t, h, o, n (one character at a time)
```

### For Loops with Conditions
```python
numbers = [3, 8, 12, 5, 20, 7]

for number in numbers:
    if number >= 10:
        print(number)
# Output: 12, 20
```

### Modulo Operator with For Loops
```python
numbers = [1, 2, 3, 4, 5, 6]

for number in numbers:
    if number % 2 == 0:
        print(number)
# Output: 2, 4, 6 (even numbers only)
```

**Important:** Zero is considered even since `0 % 2 == 0`.

### Looping Through Dictionaries
```python
student = {"name": "Ada", "age": 25, "course": "AI"}

# Looping through keys (default behavior)
for key in student:
    print(key)  # name, age, course

# Accessing both keys and values
for key in student:
    print(key, student[key])

# Using items() method - cleaner approach
for key, value in student.items():
    print(key, value)
```

### Nested Collections with For Loops
```python
students = [
    {"name": "Anna", "score": 85},
    {"name": "Bob", "score": 62},
    {"name": "Charlie", "score": 91}
]

for student in students:
    if student["score"] >= 70:
        print(student["name"], "passed")
```

### The `range()` Function
```python
# range(stop) - from 0 to stop-1
for number in range(5):
    print(number)
# Output: 0, 1, 2, 3, 4

# range(start, stop) - from start to stop-1
for number in range(2, 10):
    print(number)
# Output: 2, 3, 4, 5, 6, 7, 8, 9

# range(start, stop, step) - with step size
for number in range(2, 10, 2):
    print(number)
# Output: 2, 4, 6, 8
```

### Index vs Value Looping
```python
languages = ["Python", "Java", "C Sharp"]

# Direct iteration (recommended)
for language in languages:
    print(language)

# Using index (not recommended unless needed)
for i in range(len(languages)):
    print(i, languages[i])

# Using enumerate() - best approach when both needed
for index, language in enumerate(languages):
    print(index, language)
```

## While Loops

### Basic While Loop Structure
```python
count = 1

while count <= 5:
    print(count)
    count += 1
# Output: 1, 2, 3, 4, 5
```

**Key Points:**
- While loops continue executing as long as the condition remains `True`
- The loop variable must be updated within the loop to prevent infinite loops
- `count += 1` is equivalent to `count = count + 1`

### Infinite Loop Warning
```python
# Dangerous - creates infinite loop
count = 1
while count <= 5:
    print(count)
    # Missing: count += 1
```

**How to Stop Infinite Loops:**
- Press `Ctrl+C` (Windows/Linux) or `Command+C` (Mac) in terminal
- This sends an interrupt signal to stop execution

## Loop Control Statements

### The `break` Statement
The `break` statement immediately exits the loop when a condition is met.

```python
numbers = [2, 4, 6, 7, 8, 10]

for number in numbers:
    if number % 2 != 0:  # If odd number
        print("Found an odd number:", number)
        break
# Output: Found an odd number: 7
```

### The `continue` Statement
The `continue` statement skips the rest of the current iteration and moves to the next one.

```python
numbers = [1, 2, 3, 4, 5]

for number in numbers:
    if number == 3:
        continue
    print(number)
# Output: 1, 2, 4, 5 (3 is skipped)
```

## Best Practices Summary

### Indentation Rules
- Use exactly 4 spaces for indentation (Pythonic standard)
- Consistent indentation is crucial - all lines in the same block must have the same indentation level
- Use your IDE's auto-indentation features to maintain consistency

### Code Structure
- Prefer direct iteration (`for item in collection`) over index-based iteration when possible
- Combine conditions using logical operators (`and`, `or`, `not`)
- Order conditional statements logically to avoid unexpected behavior
- Use descriptive variable names in loops (like `language` instead of `i`)
- Handle edge cases like empty collections appropriately

### Common Patterns
- Using `in` and `not in` for membership testing
- Combining conditions with logical operators
- Using `enumerate()` when both index and value are needed
- Applying `break` and `continue` to control loop flow effectively
- Leveraging Python's truthy/falsy evaluation for cleaner code

These concepts form the foundation of program flow control in Python, enabling you to make decisions and repeat operations efficiently.