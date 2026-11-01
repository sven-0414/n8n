# Python Object-Oriented Programming

## Default Arguments and Mutable Objects

### Understanding the Problem
When using mutable objects (like lists) as default arguments in functions or methods, Python creates the object once when the function is defined, not each time it's called. This means all instances share the same object reference, leading to unexpected behavior.

### Code Example
```python
# BAD EXAMPLE - DON'T DO THIS
class BadCourse:
    def __init__(self, name, students=[]):  # Dangerous default argument
        self.name = name
        self.students = students
    
    def add_student(self, student):
        self.students.append(student)

# Demonstrating the problem
course1 = BadCourse("Python")
course2 = BadCourse("AI")

course1.add_student("Ada")
print(course1.students)  # ['Ada']
print(course2.students)  # ['Ada'] - Wait, why is Ada in course2 too?
```

### Why This Happens
Default argument values are evaluated only once when the function is defined, not each time it's called. All instances that don't explicitly provide a value for that parameter will share the same object.

### The Solution: Using None as Default
```python
# CORRECT EXAMPLE - DO THIS INSTEAD
class GoodCourse:
    def __init__(self, name, students=None):
        self.name = name
        if students is None:
            self.students = []  # Create new list each time
        else:
            self.students = students
    
    def add_student(self, student):
        self.students.append(student)

# Demonstrating the fix
course1 = GoodCourse("Python")
course2 = GoodCourse("AI")

course1.add_student("Ada")
print(course1.students)  # ['Ada']
print(course2.students)  # [] - Correctly empty
```

### Why This Matters
This is a common Python pitfall that can cause subtle bugs in your programs. Understanding this pattern helps prevent data corruption and unexpected behavior when working with shared mutable state.

## Class vs Dictionary: When to Use Each

### Dictionary Approach
```python
# Simple data storage using dictionary
student = {
    "name": "Ada",
    "score": 91
}
```

### Class Approach
```python
class Student:
    def __init__(self, name, score):
        self.name = name
        self.score = score
    
    def get_grade(self):
        if self.score >= 90:
            return "A"
        elif self.score >= 80:
            return "B"
        # ... more grading logic
```

### When to Use Each

**Use Dictionary When:**
- Simple key-value data storage
- Structure is simple and stable
- You don't need much behavior connected to the data
- Data is primarily read-only or rarely changes

**Use Class When:**
- You have many objects with the same structure
- Objects need their own behavior or state that changes over time
- You want to encapsulate related data and functions together
- You need methods to operate on the data

## Inheritance Fundamentals

### Basic Inheritance Concept
Inheritance allows classes to inherit attributes and methods from other classes. This promotes code reuse and establishes hierarchical relationships between objects.

### Code Example: Simple Inheritance
```python
# Base class (Parent/Animal)
class Animal:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def eat(self):
        print(f"{self.name} is eating")
    
    def sleep(self):
        print(f"{self.name} is sleeping")

# Derived class (Child/Dog)
class Dog(Animal):  # Inherits from Animal
    def __init__(self, name, age, breed):
        super().__init__(name, age)  # Call parent constructor
        self.breed = breed
    
    def bark(self):
        print(f"{self.name} says Woof!")

# Usage
rex = Dog("Rex", 5, "Labrador")
rex.eat()      # Uses inherited method
rex.bark()     # Uses own method
```

### Terminology in Inheritance

**Base Class/Parent Class/Super Class:** The class being inherited from
**Subclass/Child Class/Derived Class:** The class that inherits from another class

```python
# Example showing terminology
class Animal:
    def __init__(self, name):
        self.name = name
    
    def speak(self):
        print(f"{self.name} makes a sound")

class Cat(Animal):  # Cat is a subclass of Animal
    pass

# Cat inherits from Animal
# Animal is the base class
```

### Key Points About Inheritance
1. **Inheritance is directional:** Dog inherits from Animal, but Animal doesn't inherit from Dog
2. **Subclasses can use parent methods:** Objects of subclasses can call methods defined in their parent class
3. **Subclasses can override parent methods:** Subclasses can provide their own implementation
4. **Subclasses can add new functionality:** They can define additional attributes and methods

## Using `super()` for Method Resolution

### What `super()` Does
`super()` allows a subclass to call methods from its parent class. This is particularly useful when you want to extend rather than completely replace parent behavior.

### Code Example: Using `super()`
```python
class Animal:
    def __init__(self, name, age):
        self.name = name
        self.age = age
        self.is_alive = True
    
    def eat(self):
        print(f"{self.name} is eating")

class Dog(Animal):
    def __init__(self, name, age, breed):
        super().__init__(name, age)  # Call parent constructor
        self.breed = breed
    
    def eat(self):  # Override parent method
        super().eat()  # Call parent's eat method
        print(f"{self.name} is eating enthusiastically")

# Usage
rex = Dog("Rex", 3, "Golden Retriever")
rex.eat()
# Output:
# Rex is eating
# Rex is eating enthusiastically
```

### Important Details About `super()`
1. **No self parameter needed:** When calling `super().__init__()`, you don't include `self` as a parameter
2. **Method Resolution Order (MRO):** Python follows a specific order to determine which method to call in complex inheritance hierarchies

```python
class Employee:
    def __init__(self, name):
        self.name = name
    
    def get_info(self):
        return f"Employee: {self.name}"

class Developer(Employee):
    def __init__(self, name, role):
        super().__init__(name)  # No 'self' needed
        self.role = role
    
    def get_info(self):
        base_info = super().get_info()  # Call parent method
        return f"{base_info} - Role: {self.role}"

# Usage
dev = Developer("Alice", "Python Developer")
print(dev.get_info())
# Output: Employee: Alice - Role: Python Developer
```

## Method Overriding

### What is Method Overriding?
Method overriding occurs when a subclass provides a specific implementation of a method that is already defined in its parent class. The subclass can completely replace the parent's behavior or extend it.

### Code Example: Simple Override
```python
class Animal:
    def make_sound(self):
        return "Some generic animal sound"

class Dog(Animal):
    def make_sound(self):  # Override parent method
        return "Woof!"

class Cat(Animal):
    def make_sound(self):  # Override parent method
        return "Meow!"

# Usage
animal = Animal()
dog = Dog()
cat = Cat()

print(animal.make_sound())  # Some generic animal sound
print(dog.make_sound())     # Woof!
print(cat.make_sound())     # Meow!
```

### Extending Parent Behavior with `super()`
```python
class Employee:
    def __init__(self, name):
        self.name = name
    
    def get_info(self):
        return f"Employee: {self.name}"

class Developer(Employee):
    def __init__(self, name, role):
        super().__init__(name)
        self.role = role
    
    def get_info(self):
        # Extend parent behavior
        base_info = super().get_info()
        return f"{base_info} - Role: {self.role}"

# Usage
dev = Developer("Bob", "Senior Developer")
print(dev.get_info())
# Output: Employee: Bob - Role: Senior Developer
```

### Why Method Overriding Matters
1. **Polymorphism:** Different objects can respond to the same method call in their own way
2. **Code Reuse:** Inherit common behavior while customizing specific aspects
3. **Maintainability:** Changes to parent methods automatically affect all subclasses that don't override them

## Common Mistakes and Best Practices

### Mistake: Forgetting `super()` in Constructor
```python
# BAD - This will cause issues
class Dog(Animal):
    def __init__(self, name, age, breed):
        self.name = name  # Missing super() call
        self.age = age
        self.breed = breed

# GOOD - Proper way
class Dog(Animal):
    def __init__(self, name, age, breed):
        super().__init__(name, age)  # Call parent constructor
        self.breed = breed
```

### Mistake: Using Mutable Defaults
```python
# BAD
class Course:
    def __init__(self, name, students=[]):  # Don't do this!
        self.name = name
        self.students = students

# GOOD
class Course:
    def __init__(self, name, students=None):
        self.name = name
        if students is None:
            self.students = []
        else:
            self.students = students
```

### Best Practice: Clear Inheritance Relationships
```python
# Good inheritance design - "is-a" relationship
class Animal:
    def __init__(self, name):
        self.name = name

class Dog(Animal):  # Dog IS an animal
    def bark(self):
        return "Woof!"

class Cat(Animal):  # Cat IS an animal
    def meow(self):
        return "Meow!"
```

## Key Takeaways

1. **Never use mutable objects (like lists) as default arguments** - always use `None` instead
2. **Inheritance is about "is-a" relationships** - a dog is an animal, not a course has students
3. **Use `super()` to call parent methods** - especially when extending behavior rather than replacing it completely
4. **Method overriding allows subclasses to customize behavior** while maintaining common functionality
5. **Choose between classes and dictionaries based on complexity** - simple data often works better with dictionaries
6. **Understand that inheritance is directional** - child classes inherit from parent, not vice versa

This foundation in object-oriented programming will help you build more organized, maintainable code that properly leverages Python's powerful features while avoiding common pitfalls.