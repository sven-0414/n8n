# Object-Oriented Programming in Python: Inheritance, Polymorphism, and Composition

## Method Overriding and Extending Parent Behavior

### What is Method Overriding?
Method overriding occurs when a subclass provides a specific implementation of a method that is already defined in its parent class. This allows the subclass to inherit the parent's behavior while also adding or modifying functionality.

```python
class Animal:
    def make_sound(self):
        return "Some generic animal sound"

class Dog(Animal):
    def make_sound(self):
        return "Woof!"

class Cat(Animal):
    def make_sound(self):
        return "Meow!"
```

### Why Method Overriding Matters
Method overriding enables polymorphism by allowing different objects to respond to the same method call in their own way. It's a fundamental concept for creating flexible and extensible code.

## Polymorphism

### Definition of Polymorphism
Polymorphism is the ability of different objects to respond to the same method call, but in their own unique way. This allows you to write code that works with multiple types of objects through a common interface.

### Code Example
```python
class Dog:
    def make_sound(self):
        return "Woof!"

class Cat:
    def make_sound(self):
        return "Meow!"

class Cow:
    def make_sound(self):
        return "Moo!"

# Using polymorphism
animals = [Dog(), Cat(), Cow()]
for animal in animals:
    print(animal.make_sound())
# Output: Woof!
#         Meow!
#         Moo!
```

### Polymorphism with Inheritance
```python
class Animal:
    def __init__(self, name):
        self.name = name
    
    def make_sound(self):
        return "Unknown sound"

class Dog(Animal):
    def make_sound(self):
        return f"{self.name} says Woof!"

class Cat(Animal):
    def make_sound(self):
        return f"{self.name} says Meow!"

# Using polymorphism with inheritance
animals = [Dog("Rex"), Cat("Luna")]
for animal in animals:
    print(animal.make_sound())
# Output: Rex says Woof!
#         Luna says Meow!
```

### Key Rules and Gotchas
- Polymorphism works even when objects don't share a common base class, as long as they provide the same method
- The calling code doesn't need to know about specific types of objects
- This makes code more flexible and maintainable

## Duck Typing

### Definition of Duck Typing
Duck typing is a concept in Python where an object's suitability for use is determined by whether it has the required methods and properties, rather than its explicit type. The phrase "If it walks like a duck and quacks like a duck, it's a duck."

### Code Example
```python
class Robot:
    def make_sound(self):
        return "Beep!"

class Dog:
    def make_sound(self):
        return "Woof!"

# Duck typing in action
things = [Robot(), Dog()]
for thing in things:
    print(thing.make_sound())
# Output: Beep!
#         Woof!
```

### Why Duck Typing Matters
Duck typing makes Python code more flexible and reduces the need for explicit type checking. It allows for more dynamic and reusable code.

## isinstance() Function

### What is isinstance()?
The `isinstance()` function checks whether an object is an instance of a specific class or type, including inheritance relationships.

### Code Example
```python
class Animal:
    pass

class Dog(Animal):
    pass

dog = Dog()
print(isinstance(dog, Dog))      # True
print(isinstance(dog, Animal))   # True (because Dog inherits from Animal)
print(isinstance(dog, str))      # False
```

### Key Rules and Gotchas
- `isinstance()` understands inheritance relationships
- It's useful when you genuinely need to check type relationships
- However, avoid overusing it in favor of polymorphism where possible

## Special Methods: __str__() Method

### What is __str__()?
The `__str__()` method defines the string representation of an object. When you print an object or convert it to a string using `str()`, Python calls this method.

### Code Example
```python
class Student:
    def __init__(self, name, score):
        self.name = name
        self.score = score
    
    def __str__(self):
        return f"Student: {self.name}, Score: {self.score}"

student = Student("Alice", 85)
print(student)  # Output: Student: Alice, Score: 85
```

### Why __str__() Matters
The `__str__()` method makes objects more human-readable and useful for debugging and logging. It's part of Python's special method system that allows you to control how objects behave in different contexts.

### Inheritance with Special Methods
```python
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary
    
    def __str__(self):
        return f"Employee: {self.name}, Salary: ${self.salary}"

class Developer(Employee):
    def __init__(self, name, salary, language):
        super().__init__(name, salary)
        self.language = language
    
    def __str__(self):
        return f"Developer: {self.name}, Salary: ${self.salary}, Language: {self.language}"

employee = Employee("Grace", 45000)
developer = Developer("Ada", 60000, "Python")

print(employee)  # Output: Employee: Grace, Salary: $45000
print(developer) # Output: Developer: Ada, Salary: $60000, Language: Python
```

## Composition vs Inheritance

### Definition of Composition
Composition is a design principle where one class contains or uses objects of other classes as part of its internal structure. It represents a "has-a" relationship rather than an "is-a" relationship.

### Code Example
```python
class Engine:
    def __init__(self, horsepower):
        self.horsepower = horsepower

class Car:
    def __init__(self, brand, engine):
        self.brand = brand
        self.engine = engine
    
    def get_info(self):
        return f"{self.brand} with {self.engine.horsepower} horsepower engine"

# Composition in action
engine = Engine(200)
car = Car("Volvo", engine)
print(car.get_info())  # Output: Volvo with 200 horsepower engine
```

### Inheritance vs Composition Decision Matrix

#### Use Inheritance When:
- There's a clear "is-a" relationship
- You want to extend or modify parent class behavior
- The relationship represents a natural hierarchy (Dog is an Animal)

```python
class Animal:
    def __init__(self, name):
        self.name = name

class Dog(Animal):
    def make_sound(self):
        return "Woof!"
```

#### Use Composition When:
- There's a "has-a" relationship
- You want to reuse functionality without creating inheritance hierarchies
- Objects contain other objects (Car has an Engine)

```python
class Engine:
    def __init__(self, power):
        self.power = power

class Car:
    def __init__(self, brand, engine):
        self.brand = brand
        self.engine = engine  # Composition
```

### Key Principles for Choosing Between Inheritance and Composition

1. **"Is-a" vs "Has-a" Relationship**: 
   - Use inheritance when one class IS another class (Dog IS an Animal)
   - Use composition when one class HAS another class (Car HAS an Engine)

2. **Avoid Inheritance for Code Reuse Alone**:
```python
# Bad: Inheritance based only on similar code
class Printer:
    def get_data(self):
        return "printer data"

class Student:
    def get_data(self):
        return "student data"
    
# Instead, consider composition or a common interface
```

3. **Design for Relationships, Not Just Code Duplication**:
   - Focus on the conceptual relationship between classes
   - Don't choose inheritance simply to avoid duplicate code
   - Consider whether the relationship actually makes sense conceptually

### Why This Distinction Matters
Understanding when to use inheritance versus composition leads to better-designed software that's more maintainable and flexible. It prevents tight coupling between classes and makes your code more robust to changes.

## Summary of Key Concepts

1. **Method Overriding**: Subclasses can provide their own implementation of parent methods
2. **Polymorphism**: Different objects respond to the same method call in their own way
3. **Duck Typing**: Focus on behavior rather than explicit type checking
4. **isinstance()**: Check object type relationships including inheritance
5. **__str__() Method**: Control how objects appear when printed or converted to string
6. **Composition vs Inheritance**: Choose based on conceptual relationships, not just code reuse

These concepts form the foundation of object-oriented programming in Python and enable you to write flexible, maintainable, and extensible code.