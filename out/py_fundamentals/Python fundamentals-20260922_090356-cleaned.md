# Object-Oriented Programming in Python

## Classes and Objects

### What is a Class?
A **class** is a definition or blueprint that describes what data and behavior an object of that type will have. It serves as a template for creating objects.

```python
class Student:
    pass
```

### What is an Object?
An **object** is a concrete instance created from a class. Each object has its own state and behavior defined by the class blueprint.

```python
student1 = Student()
student2 = Student()
```

### Key Concepts
- One class can be used to create many objects
- Objects created from the same class are separate instances
- Objects have their own memory location and are not identical even if they share the same class

## Creating Your First Class

### Basic Class Structure
```python
class Student:
    pass

student1 = Student()
student2 = Student()
print(type(student1))  # <class '__main__.Student'>
```

### Pascal Case Naming Convention
Class names should use PascalCase (first letter capitalized):
```python
class BankAccount:
    pass

class Student:
    pass
```

## Instance Attributes and Initialization

### Adding Information Manually (Problematic Approach)
```python
student1 = Student()
student1.name = "Ada"
student1.score = 91

student2 = Student()
student2.name = "Grace"
student2.score = 85
```

**Problem**: This approach requires manually setting attributes each time you create an object, which is error-prone and inefficient.

### Using `__init__` Method (Dunder Init)
The `__init__` method is a special method that Python calls automatically when creating a new object:

```python
class Student:
    def __init__(self, name, score):
        self.name = name
        self.score = score

student1 = Student("Ada", 91)
student2 = Student("Grace", 85)
```

### Understanding `self`
The `self` parameter refers to the specific object being created or worked with:

```python
class Student:
    def __init__(self, name, score):
        self.name = name  # This sets the attribute on the specific object
        self.score = score

# When student1 = Student("Ada", 91) is called:
# self refers to student1
# self.name = "Ada" sets the name attribute on student1
# self.score = 91 sets the score attribute on student1
```

**Important Points about `self`**:
- `self` is a naming convention, not a keyword
- It's automatically passed by Python when calling methods
- It must be the first parameter in instance methods
- Each object has its own `self` reference

### Default Parameters in `__init__`
```python
class Student:
    def __init__(self, name, score=0, active=True):
        self.name = name
        self.score = score
        self.active = active

student1 = Student("Ada", 91)  # Uses default active=True
student2 = Student("Bob")       # Uses all defaults: score=0, active=True
```

### Keyword Arguments
```python
student3 = Student(name="Grace", score=88, active=False)
```

## Methods

### What are Methods?
Methods are functions defined inside a class that can access and modify the object's attributes. They always take `self` as their first parameter.

```python
class Student:
    def __init__(self, name, score):
        self.name = name
        self.score = score
    
    def introduce(self):
        print(f"Hello, my name is {self.name}")

student1 = Student("Ada", 91)
student1.introduce()  # Output: Hello, my name is Ada
```

### Methods That Return Values
```python
class Student:
    def __init__(self, name, score):
        self.name = name
        self.score = score
    
    def get_status(self):
        if self.score >= 70:
            return "Pass"
        else:
            return "Fail"

student1 = Student("Ada", 91)
status = student1.get_status()  # Returns "Pass"
```

### Methods That Modify Object State
```python
class Student:
    def __init__(self, name, score):
        self.name = name
        self.score = score
    
    def update_score(self, new_score):
        self.score = new_score

student1 = Student("Ada", 80)
print(student1.score)  # Output: 80
student1.update_score(95)
print(student1.score)  # Output: 95
```

### Validation in Methods
```python
class Student:
    def __init__(self, name, score=0):
        self.name = name
        self.score = score
    
    def update_score(self, new_score):
        if new_score < 0 or new_score > 100:
            raise ValueError("Score must be between 0 and 100")
        self.score = new_score

student1 = Student("Ada", 80)
student1.update_score(95)  # Works fine
# student1.update_score(150)  # Raises ValueError
```

## Class Attributes vs Instance Attributes

### Instance Attributes
Instance attributes belong to specific objects:

```python
class Student:
    def __init__(self, name):
        self.name = name  # This is an instance attribute

student1 = Student("Ada")
student2 = Student("Grace")

student1.name = "Bob"  # Only changes student1's name
print(student1.name)   # Output: Bob
print(student2.name)   # Output: Grace
```

### Class Attributes
Class attributes are shared by all instances of the class:

```python
class Student:
    school = "Lexicon"  # This is a class attribute
    
    def __init__(self, name):
        self.name = name  # This is an instance attribute

student1 = Student("Ada")
student2 = Student("Grace")

print(student1.school)  # Output: Lexicon
print(student2.school)  # Output: Lexicon

Student.school = "Academy"  # Changes class attribute
print(student1.school)      # Output: Academy
print(student2.school)      # Output: Academy

student1.school = "Another School"  # Creates instance attribute that shadows class attribute
print(student1.school)              # Output: Another School
print(student2.school)              # Output: Academy
```

### Practical Example with Class Attributes
```python
class Product:
    tax_rate = 0.25  # Class attribute - shared by all products
    
    def __init__(self, name, price):
        self.name = name
        self.price = price
    
    def price_with_tax(self):
        return self.price * (1 + self.tax_rate)

product1 = Product("Keyboard", 800)
product2 = Product("Mouse", 300)

print(product1.price_with_tax())  # Output: 1000.0
print(product2.price_with_tax())  # Output: 375.0
```

## Collections of Objects

### Storing Objects in Lists
```python
class Student:
    def __init__(self, name, score):
        self.name = name
        self.score = score
    
    def get_status(self):
        return "Pass" if self.score >= 70 else "Fail"

students = [
    Student("Anna", 85),
    Student("Bob", 62),
    Student("Charlie", 91)
]

# Loop through objects in list
for student in students:
    print(f"{student.name}: {student.get_status()}")

# List comprehension to filter objects
passing_students = [student for student in students if student.score >= 70]
for student in passing_students:
    print(student.name)  # Output: Anna, Charlie
```

## Object Composition

### Objects Containing Other Objects
```python
class Teacher:
    def __init__(self, name):
        self.name = name

class Course:
    def __init__(self, name, teacher):
        self.name = name
        self.teacher = teacher  # This is another object
    
    def get_teacher_name(self):
        return self.teacher.name

teacher = Teacher("Grace")
course = Course("Python Foundation", teacher)

print(course.name)           # Output: Python Foundation
print(course.teacher.name)   # Output: Grace
```

### Objects Containing Lists of Other Objects
```python
class Student:
    def __init__(self, name):
        self.name = name

class Course:
    def __init__(self, name):
        self.name = name
        self.students = []  # List containing student objects
    
    def add_student(self, student):
        self.students.append(student)
    
    def list_students(self):
        for student in self.students:
            print(student.name)

course = Course("Python Foundation")
student1 = Student("Ada")
student2 = Student("Grace")

course.add_student(student1)
course.add_student(student2)
course.list_students()  # Output: Ada, Grace
```

## Why Object-Oriented Programming Matters

### Benefits of OOP:
1. **Organization**: Groups related data and behavior together
2. **Reusability**: Classes can be reused to create multiple objects
3. **Maintainability**: Changes to one class affect all instances consistently
4. **Encapsulation**: Data and methods are bundled together
5. **Abstraction**: Complex systems can be broken down into manageable components

### Comparison with Functional Approach:
While you could accomplish similar tasks using dictionaries and functions, OOP provides a more structured and intuitive way to model real-world entities with both data and behavior.

```python
# Without OOP (functional approach)
student1 = {"name": "Ada", "score": 91, "active": True}
student2 = {"name": "Grace", "score": 85, "active": True}

def get_status(student):
    return "Pass" if student["score"] >= 70 else "Fail"

# With OOP (object-oriented approach)
class Student:
    def __init__(self, name, score):
        self.name = name
        self.score = score
    
    def get_status(self):
        return "Pass" if self.score >= 70 else "Fail"
```

The OOP approach makes the relationship between data and behavior more explicit and easier to understand.