## Python Fundamentals

### Introduction
- **Purpose**: The goal of this course is to ensure everyone has a solid foundation in Python before diving into data analysis, AI, and machine learning.
- **Background**: Python was created by Guido van Rossum and was first released in the early 1990s. Its main focus is on readability and simplicity, making it an ideal language for beginners and experts alike.

### Setting Up the Environment
- **Python Installation**: Ensure Python is installed correctly. You can verify by running `python` in the terminal to start the Python interpreter.
- **Interactive Shell**: Use the Python interactive shell to test snippets of code.
    ```python
    >>> print("Hello World")
    Hello World
    ```

### Basic Syntax and Data Types

#### Variables
- **Dynamic Typing**: Python automatically determines the data type of a variable based on its assigned value.
    ```python
    number = 10
    print(type(number))
    ```
- **Variable Naming Conventions**: Follow snake_case (e.g., `snake_case`) and avoid starting with numbers or special characters.

#### Data Types
- **Integer** (`int`)
- **Float** (`float`)
- **String** (`str`)
- **Boolean** (`bool`): True and False are case-sensitive.

### Arithmetic Operators
- **Examples**:
    ```python
    print(5 + 2)   # 7
    print(5 - 2)   # 3
    print(5 * 2)   # 10
    print(5 / 2)   # 2.5
    print(5 ** 2)  # 25
    print(5 // 2)  # 2 (Floor division)
    print(5 % 2)   # 1 (Modulus - remainder)
    ```

### Type Conversion
- **Converting Data Types**:
    ```python
    number_as_text = '10'
    number = int(number_as_text)
    print(number + 5)  # 15
    ```

### Strings
- **Creation and Concatenation**:
    ```python
    first_name = 'Ada'
    last_name = 'Addison'
    full_name = first_name + ' ' + last_name
    print(full_name)  # Ada Addison
    ```
- **Slicing and Indexing**:
    ```python
    text = 'python'
    print(text[0:3])  # pyt
    print(text[-1])   # n
    ```
- **String Methods**:
    ```python
    message = " Hello Python "
    print(message.lower())  # hello python
    print(message.strip())  # Hello Python
    print(message.replace("Python", "Powerful"))  # Hello Powerful
    ```

### Immutability of Strings
- **Changing a String**:
    ```python
    word = 'python'
    word = word[0].replace('p', 'j') + word[1:]
    print(word)  # jython
    ```

### Lists
- **Introduction**: Lists are mutable sequences used to store multiple items. 
- **Creating Lists**:
    ```python
    data = ['apple', 'banana', 'orange']
    print(len(data))  # 3
    ```

### Conclusion
- **Next Steps**: The next lesson will focus on lists and their operations, including slicing, indexing, and mutability.

### Additional Notes
- **AI Usage**: Some examples and labs may have been created or refined using AI tools to enhance clarity and educational value.
- **Lab Instructions**: Labs will be available in a specific folder within the Python Info chat. Follow instructions carefully and complete the lab to reinforce concepts covered in the lecture.