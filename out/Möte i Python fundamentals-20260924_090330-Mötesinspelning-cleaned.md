## Method Overriding and Polymorphism

### Method Overriding
Method overriding is a feature of object-oriented programming that allows a subclass to provide a specific implementation of a method that is already defined in its superclass. This means the subclass can redefine a method that it inherits from the superclass, altering its behavior while still using the same method name.

#### Example
```python
class Animal:
    def get_info(self):
        return "I am an animal"

class Dog(Animal):
    def get_info(self):
        return super().get_info() + ", and I am a dog"

dog = Dog()
print(dog.get_info())  # Output: I am an animal, and I am a dog
```

### Polymorphism
Polymorphism in Python is the ability of objects to respond differently to the same method call. This means that objects of different classes can be treated as objects of a common superclass, and the specific method that is executed depends on the type of the object.

#### Example
```python
class Dog:
    def make_sound(self):
        return "Woof"

class Cat:
    def make_sound(self):
        return "Meow"

animals = [Dog(), Cat()]
for animal in animals:
    print(animal.make_sound())
```

### Duck Typing
Duck typing is a concept where the type or the class of an object is less important than the methods it defines and whether those methods are being called correctly. If an object implements the methods of another class, it can be used in its place.

#### Example
```python
class Robot:
    def make_sound(self):
        return "Beep"

class Dog:
    def make_sound(self):
        return "Woof"

robot = Robot()
dog = Dog()
for thing in [robot, dog]:
    print(thing.make_sound())
```

### Checking Object Type
In Python, you can check if an object is an instance of a specific class or inherits from a class using `isinstance()`.

#### Example
```python
class Animal:
    pass

class Dog(Animal):
    pass

dog = Dog()
print(isinstance(dog, Animal))  # Output: True
print(isinstance(dog, Dog))     # Output: True
print(isinstance(dog, str))     # Output: False
```

## String Representation (`__str__` Method)

### Default Behavior
By default, when you print an object, Python displays its class name and a unique identifier.

#### Example
```python
class Student:
    def __init__(self, name, score):
        self.name = name
        self.score = score

student = Student("Ada", 91)
print(student)  # Output: <__main__.Student object at 0x7f8b9c1b9b00>
```

### Customizing Object Representation
You can customize the string representation of an object by defining the `__str__` method in your class.

#### Example
```python
class Student:
    def __init__(self, name, score):
        self.name = name
        self.score = score

    def __str__(self):
        return f"Name: {self.name}, Score: {self.score}"

student = Student("Ada", 91)
print(student)  # Output: Name: Ada, Score: 91
```

### Overriding `__str__` in Subclasses
Subclasses can override the `__str__` method to provide their own representation.

#### Example
```python
class Employee:
    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    def __str__(self):
        return f"Name: {self.name}, Salary: {self.salary}"

class Developer(Employee):
    def __init__(self, name, salary, language):
        super().__init__(name, salary)
        self.language = language

    def __str__(self):
        return f"Name: {self.name}, Salary: {self.salary}, Language: {self.language}"

employee = Employee("Grace", 45000)
developer = Developer("Ada", 50000, "Python")
print(employee)  # Output: Name: Grace, Salary: 45000
print(developer) # Output: Name: Ada, Salary: 50000, Language: Python
```

## Inheritance vs. Composition

### Inheritance
Inheritance is useful when you want to create a new class based on an existing class. The new class inherits all the properties and methods from the existing class and can add or override them.

### Composition
Composition is when an object contains another object as a component. This is used when one class represents a "has-a" relationship rather than an "is-a" relationship.

#### Example
```python
class Engine:
    def __init__(self, power):
        self.power = power

class Car:
    def __init__(self, brand, engine):
        self.brand = brand
        self.engine = engine

engine = Engine(200)
car = Car("Volvo", engine)
print(car.engine.power)  # Output: 200
```

### Choosing Between Inheritance and Composition
- Use inheritance when there is a clear "is-a" relationship.
- Use composition when there is a "has-a" relationship.
- Avoid using inheritance just to reuse code; ensure the relationship makes logical sense.

### Summary
- **Polymorphism** allows objects of different classes to be treated uniformly.
- **Duck typing** focuses on the methods an object provides rather than its exact class.
- **Inheritance** and **composition** both have their roles, but choose the one that fits the problem's logical structure.