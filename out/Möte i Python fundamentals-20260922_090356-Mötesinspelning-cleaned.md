## Introduction to Object-Oriented Programming (OOP) in Python

### Overview
In Python, Object-Oriented Programming (OOP) allows us to model real-world entities as software objects, encapsulating data and behavior within a class. This approach provides a clear structure for managing complex data and operations.

### Class and Object
- **Class**: A blueprint or template for creating objects. It defines a set of attributes (data) and methods (functions) that will be shared by all objects created from that class.
- **Object**: A unique instance of a class. Each object has its own set of attribute values.

### Creating a Class
```python
class Student:
    pass
```
**Gotchas:**
- Use PascalCase for class names (e.g., `Student`).
- The `pass` statement is used when the class is initially empty.

### Creating Objects
```python
student_one = Student()
```
**Gotchas:**
- Use the class name followed by parentheses to create an object.

### Attributes
Attributes are variables that store data for an object.
```python
student_one.name = "Ada"
student_one.score = 91
```
**Gotchas:**
- Attributes must be accessed using dot notation (e.g., `student_one.name`).

### Problem with Manual Attribute Addition
Creating objects manually with attributes can be error-prone and repetitive.

### `__init__` Method
The `__init__` method is a special method used to initialize new objects when they are created.

#### Example
```python
class Student:
    def __init__(self, name, score):
        self.name = name
        self.score = score
```
**Explanation:**
- `self` refers to the instance being created.
- `name` and `score` are parameters that will be passed when creating a new object.

#### Creating Objects with `__init__`
```python
student_one = Student("Ada", 91)
```

### Self Parameter
- `self` is a convention for the first parameter of instance methods, referring to the instance itself.
- `self` can be named anything, but it is a good practice to use `self`.

### Methods
Methods are functions defined inside a class that operate on objects created from that class.

#### Example
```python
class Student:
    def __init__(self, name, score):
        self.name = name
        self.score = score
    
    def introduce(self):
        print(f"Hello, my name is {self.name}")
```

#### Calling Methods
```python
student_one = Student("Ada", 91)
student_one.introduce()
```

### Instance Attributes vs Class Attributes
- **Instance Attributes**: Unique to each object.
- **Class Attributes**: Shared among all objects of the class.

#### Example
```python
class Student:
    school = "Lexicon"  # Class attribute
    
    def __init__(self, name, score):
        self.name = name
        self.score = score
```

### Modifying Class Attributes
- Changing a class attribute affects all instances unless an instance-specific attribute overrides it.
```python
Student.school = "AI Academy"
```

### Methods Returning Values
Methods can return values just like functions.
```python
class Student:
    def get_status(self):
        if self.score >= 70:
            return "Pass"
        else:
            return "Fail"
```

### Methods Changing State
Methods can change the state of an object by modifying its attributes.
```python
class Student:
    def update_score(self, new_score):
        if new_score < 0 or new_score > 100:
            raise ValueError("Score must be between 0 and 100")
        self.score = new_score
```

### Collections of Objects
- Storing objects in lists and iterating over them.
- Example:
```python
students = [Student("Ada", 85), Student("Bob", 62), Student("Charlie", 91)]

for student in students:
    print(f"{student.name} {student.get_status()}")
```

### Complex Objects
Objects can contain other objects or collections of objects.
```python
class Course:
    def __init__(self, name):
        self.name = name
        self.students = []

    def add_student(self, student):
        self.students.append(student)
```

### Conclusion
Understanding classes, objects, attributes, and methods is crucial for effective OOP in Python. This foundational knowledge allows for the creation of robust and maintainable code structures.