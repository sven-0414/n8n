# Python Collections: Lists, Tuples, Sets, and Dictionaries

## Lists

### Definition and Properties
A list is an ordered collection of items that can contain multiple values. Lists are mutable, meaning you can change their content after creation. This is in contrast to immutable types like strings.

### Creating Lists
Lists are created using square brackets `[]` with elements separated by commas:

```python
numbers = [10, 20, 30, 40, 50]
names = ["Alice", "Bob", "Charlie"]
mixed = [10, "hello", True, 3.14]
```

### Indexing and Slicing
Lists use zero-based indexing like strings, with the same slicing syntax:
```python
numbers = [10, 20, 30, 40, 50]
print(numbers[0])    # Output: 10 (first element)
print(numbers[1])    # Output: 20 (second element)
print(numbers[-1])   # Output: 50 (last element)
print(numbers[1:4])  # Output: [20, 30, 40] (elements from index 1 to 3, stop not included)
```

### List Mutability
Lists are mutable, allowing you to modify elements directly:
```python
names = ["Alice", "Bob", "Charlie"]
names[1] = "David"   # Change element at index 1
print(names)         # Output: ['Alice', 'David', 'Charlie']
```

### List Methods
Several useful methods for working with lists:

#### append()
Adds an element to the end of the list:
```python
languages = ["Python", "Java"]
languages.append("JavaScript")
print(languages)  # Output: ['Python', 'Java', 'JavaScript']
```

#### insert()
Inserts an element at a specific index:
```python
languages = ["Python", "Java"]
languages.insert(1, "Go")
print(languages)  # Output: ['Python', 'Go', 'Java']
```

#### remove()
Removes the first occurrence of a value:
```python
languages = ["Python", "Java", "JavaScript"]
languages.remove("Java")
print(languages)  # Output: ['Python', 'JavaScript']
```

#### pop()
Removes and returns an element at a specific index (or last element if no index given):
```python
languages = ["Python", "Java", "JavaScript"]
removed = languages.pop(1)
print(removed)    # Output: Java
print(languages)  # Output: ['Python', 'JavaScript']
```

### Sorting and Reversing
#### sort()
Sorts the list in place (modifies original):
```python
numbers = [5, 2, 9, 1]
numbers.sort()
print(numbers)  # Output: [1, 2, 5, 9]
```

#### reverse()
Reverses the list in place:
```python
numbers = [1, 2, 3]
numbers.reverse()
print(numbers)  # Output: [3, 2, 1]
```

### sorted() vs sort()
The key difference is that `sorted()` returns a new sorted list while `sort()` modifies the original list:

```python
original = [5, 2, 9, 1]
new_list = sorted(original)
print(original)   # Output: [5, 2, 9, 1] (unchanged)
print(new_list)   # Output: [1, 2, 5, 9]

original.sort()
print(original)   # Output: [1, 2, 5, 9] (changed)
```

### Copying Lists
When you assign a list to another variable, both variables reference the same list object:

```python
list_a = [1, 2, 3]
list_b = list_a      # Both reference the same list
list_b.append(4)
print(list_a)        # Output: [1, 2, 3, 4] (changed!)
```

To create an independent copy, use `.copy()` method:
```python
list_a = [1, 2, 3]
list_b = list_a.copy()  # Independent copy
list_b.append(4)
print(list_a)           # Output: [1, 2, 3] (unchanged)
```

## Tuples

### Definition and Properties
A tuple is an ordered, immutable collection. Like lists, tuples support indexing, but unlike lists, they cannot be modified after creation.

### Creating Tuples
Tuples are created using parentheses `()`:
```python
coordinates = (10, 20)
person = ("Alice", 30, "London")
```

### Tuple Indexing and Slicing
Works exactly like lists:
```python
coordinates = (10, 20, 30)
print(coordinates[0])   # Output: 10
print(coordinates[-1])  # Output: 30
print(coordinates[1:3]) # Output: (20, 30)
```

### Tuple Unpacking
You can unpack tuples into separate variables:
```python
coordinates = (10, 20)
x, y = coordinates
print(x)  # Output: 10
print(y)  # Output: 20
```

### Tuple Swapping
Python's multiple assignment allows easy swapping without temporary variables:
```python
a = 10
b = 20
a, b = b, a
print(a)  # Output: 20
print(b)  # Output: 10
```

### Why Use Tuples?
- **Immutability**: Communicates that data shouldn't change
- **Performance**: Faster execution and lighter memory usage
- **Fixed Data**: Good for representing fixed structures like coordinates

## Sets

### Definition and Properties
A set is an unordered collection of unique elements. Sets automatically remove duplicates and do not support indexing.

### Creating Sets
Sets are created using curly braces `{}` or the `set()` function:
```python
numbers = {1, 2, 3, 4}
unique_names = {"Alice", "Bob", "Charlie"}
empty_set = set()  # Not {} - that creates an empty dictionary!
```

### Set Operations
#### Adding and Removing Elements
```python
languages = {"Python", "Java"}
languages.add("JavaScript")     # Add element
languages.remove("Java")        # Remove element (raises KeyError if not found)
languages.discard("C++")        # Remove element (no error if not found)
```

#### Set Operations
```python
backend_languages = {"Python", "Java", "C#"}
data_languages = {"Python", "R", "Julia"}

# Intersection (elements in both sets)
intersection = backend_languages & data_languages  # Output: {'Python'}

# Union (all elements from both sets)
union = backend_languages | data_languages  # Output: {'Python', 'Java', 'C#', 'R', 'Julia'}

# Difference (elements in first set but not second)
difference = backend_languages - data_languages  # Output: {'Java', 'C#'}
```

### Important Characteristics
- **No indexing**: You cannot access elements by index
- **No duplicates**: Automatically removes duplicate values
- **Unordered**: Elements have no fixed position
- **Membership testing**: Fast checking if an element exists in the set

## Dictionaries

### Definition and Properties
A dictionary stores data in key-value pairs. Keys are unique and immutable, while values can be any type.

### Creating Dictionaries
```python
person = {"name": "Alice", "age": 30, "city": "London"}
empty_dict = {}  # Empty dictionary
```

### Accessing Values
Values are accessed using keys:
```python
person = {"name": "Alice", "age": 30}
print(person["name"])    # Output: Alice
print(person["age"])     # Output: 30
```

### Modifying Dictionaries
```python
person = {"name": "Alice", "age": 30}
person["age"] = 31        # Change existing value
person["job"] = "Engineer" # Add new key-value pair
print(person)             # Output: {'name': 'Alice', 'age': 31, 'job': 'Engineer'}
```

### Dictionary Methods
#### get()
Safe way to access values with default fallback:
```python
person = {"name": "Alice", "age": 30}
city = person.get("city", "Unknown")  # Output: Unknown
```

#### keys(), values(), items()
```python
person = {"name": "Alice", "age": 30, "city": "London"}
print(person.keys())    # Output: dict_keys(['name', 'age', 'city'])
print(person.values())  # Output: dict_values(['Alice', 30, 'London'])
print(person.items())   # Output: dict_items([('name', 'Alice'), ('age', 30), ('city', 'London')])
```

### Nested Collections
Collections can be nested to create complex data structures:
```python
students = [
    {"name": "Alice", "score": 85},
    {"name": "Bob", "score": 72},
    {"name": "Charlie", "score": 91}
]
print(students[1]["score"])  # Output: 72 (Bob's score)
```

## Key Concepts and Gotchas

### Mutability Differences
- **Lists**: Mutable - can be changed after creation
- **Tuples**: Immutable - cannot be modified after creation
- **Sets**: Mutable - elements can be added/removed but not indexed
- **Dictionaries**: Mutable - values can be changed, keys remain the same

### Common Mistakes
1. **Empty collections confusion**: `{}` creates an empty dictionary, not set
2. **Indexing sets**: Sets don't support indexing - use loops or convert to list first
3. **Key duplication**: Adding a key that already exists replaces the value
4. **Reference vs copy**: Assignment creates references, not copies

### Why These Matter
Understanding these collection types helps you choose the right tool for specific tasks:
- Use lists for ordered data that may change
- Use tuples for fixed data structures like coordinates
- Use sets for unique elements and membership testing
- Use dictionaries for key-value mappings and fast lookups