## Default Arguments in Classes

### Problem: Shared Default List

When creating a class in Python, default arguments can lead to unexpected behavior if you use mutable types (like lists) as default values.

- **Rule**: When defining a class with default arguments that are mutable objects (e.g., lists or dictionaries), these objects are created once and shared among all instances created from the class.
- **Example**:
    ```python
    class Course:
        def __init__(self, name, students=[]):
            self.name = name
            self.students = students
    ```
- **Gotchas**:
    - If you use a mutable object like a list as a default argument, all instances will share the same list.
    - This can cause unintended side effects when modifying the shared list.

### Solution: Use `None` and Initialize Inside `__init__`

To avoid sharing mutable default arguments, you should initialize the default argument with `None` and then create the default value inside the `__init__` method.

- **Rule**: Define the default argument as `None` and then initialize it to an empty list inside the `__init__` method.
- **Example**:
    ```python
    class Course:
        def __init__(self, name, students=None):
            self.name = name
            self.students = [] if students is None else students
    ```

### Why This Matters

Using `None` ensures that each instance of the class will have its own separate list, avoiding the pitfalls of shared mutable default arguments.

## Inheritance in Python

### Concept: Inheritance

Inheritance allows a subclass to inherit attributes and methods from a superclass (base class).

- **Rule**: Use `class SubClass(BaseClass)` to define a subclass that inherits from a superclass.
- **Example**:
    ```python
    class Animal:
        def eat(self):
            print(f"{self.name} is eating.")

    class Dog(Animal):
        def bark(self):
            print(f"{self.name} says woof!")
    ```

### Method Overriding

When a subclass provides a different implementation for a method that it inherited from its superclass, it is called method overriding.

- **Rule**: Override methods by defining them in the subclass with the same signature as the superclass method.
- **Example**:
    ```python
    class Dog(Animal):
        def eat(self):
            super().eat()
            print(f"{self.name} eats dog food.")
    ```

### Polymorphism

Polymorphism allows different objects to be treated as instances of their base class, allowing methods to be called without knowing the specific subclass.

- **Rule**: Use `super()` to call a method from the superclass within the subclass to ensure the base method is executed.
- **Example**:
    ```python
    class Employee:
        def get_info(self):
            return "Employee Info"

    class Developer(Employee):
        def get_info(self):
            base_info = super().get_info()
            return f"{base_info} - Developer"
    ```

### Why This Matters

Inheritance allows for code reuse and abstraction, while polymorphism provides flexibility and modularity in object-oriented design.

## Dictionaries vs. Classes

### When to Use Dictionaries

Dictionaries are a simple key-value store and can be used instead of classes when you don't need behavior or state change over time.

- **Rule**: Use a dictionary when the structure is simple and you don't need behavior connected to the data.
- **Example**:
    ```python
    student = {"name": "Ada", "score": 91}
    ```

### When to Use Classes

Classes should be used when objects need to have behavior or state that changes over time.

- **Rule**: Use classes when objects need methods, and the structure or behavior is complex enough to warrant encapsulation.
- **Example**:
    ```python
    class Student:
        def __init__(self, name, score):
            self.name = name
            self.score = score
    ```

### Why This Matters

Choosing between dictionaries and classes is crucial for maintaining clean, modular code and ensuring that your data structures are appropriate for the task at hand.