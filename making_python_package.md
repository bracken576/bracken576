# Building Python Packages with pyproject.toml

A comprehensive guide to creating and deploying Python packages using the modern `pyproject.toml` approach.

I got a lot of inspiration from this [Medium article](https://medium.com/@ebimsv/building-python-packages-07fbfbb959a9) by Ebrahim Mousavi and then tried to explain it myself and provide a different example.

## What is pyproject.toml?

`pyproject.toml` is the modern, standardized way to configure Python projects, introduced in PEP 518. The previous configuration was done with `setup.py` and was done through a Python package called `setuptools`. With `pyproject.toml` it is done through the toml language which is a lot more human readable and allows for better understanding of the structure of a package. 

### Key Advantages:
- **Tool Compatibility**: Works seamlessly with various packaging tools
- **Clear Separation**: Separates build configuration from package metadata
- **Declarative Format**: Uses TOML format for better readability

## Basic pyproject.toml Structure

```toml
[build-system]
requires = ["setuptools>=42", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "mypackage"
version = "0.1.0"
description = "A brief description of your package"
dependencies = ["requests"]
```

## Complete Example: MathCalculator Package

Let's build a simple calculator package that performs basic mathematical operations.

### 1. Project Structure

```
mathcalculator/
│
├── mathcalculator/
│   ├── __init__.py
│   ├── calculator.py
│
├── tests/
│   ├── test_calculator.py
│
├── LICENSE
├── README.md
├── pyproject.toml
└── .gitignore
```

### 2. Writing the Code

**mathcalculator/calculator.py:**
```python
class MathCalculator:
    """A simple calculator for basic mathematical operations."""
    
    def add(self, a, b):
        """Add two numbers."""
        return a + b
    
    def subtract(self, a, b):
        """Subtract second number from first number."""
        return a - b
    
    def multiply(self, a, b):
        """Multiply two numbers."""
        return a * b
    
    def divide(self, a, b):
        """Divide first number by second number."""
        if b == 0:
            raise ValueError("Cannot divide by zero!")
        return a / b
```

**`mathcalculator/ __init__.py:`**
```python
from .calculator import MathCalculator

__all__ = ["MathCalculator"]
__version__ = "0.1.0"
```

### 3. Creating pyproject.toml

```toml
[build-system]
requires = ["setuptools>=42", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "mathcalculator"
version = "0.1.0"
description = "A simple calculator package for basic mathematical operations"
authors = [
    {name = "Bracken", email = "bracken576@outlook.com"}
]
license = {text = "MIT"}
readme = "README.md"
requires-python = ">=3.6"
dependencies = []

[project.urls]
"Homepage" = "https://github.com/brcken576/mathcalculator"

[project.optional-dependencies]
dev = ["pytest>=6.0"]
```

#### Configuration Breakdown:

- **[build-system]**: Specifies build requirements and backend
- **[project]**: Contains package metadata
  - `name`: Package name (must be unique on PyPI)
  - `version`: Package version following semantic versioning
  - `description`: Brief description of the package
  - `authors`: List of authors with name and email
  - `license`: License information
  - `readme`: Path to README file
  - `requires-python`: Minimum Python version requirement
  - `dependencies`: Runtime dependencies

### 4. Writing Tests

**tests/test_calculator.py:**
```python
import pytest
from mathcalculator import MathCalculator

def test_addition():
    calc = MathCalculator()
    assert calc.add(2, 3) == 5
    assert calc.add(-1, 1) == 0

def test_subtraction():
    calc = MathCalculator()
    assert calc.subtract(5, 3) == 2
    assert calc.subtract(1, 1) == 0

def test_multiplication():
    calc = MathCalculator()
    assert calc.multiply(3, 4) == 12
    assert calc.multiply(-2, 3) == -6

def test_division():
    calc = MathCalculator()
    assert calc.divide(10, 2) == 5
    assert calc.divide(7, 2) == 3.5

def test_division_by_zero():
    calc = MathCalculator()
    with pytest.raises(ValueError, match="Cannot divide by zero!"):
        calc.divide(5, 0)
```

### 5. README.md

```markdown
# MathCalculator

A simple Python package for basic mathematical operations.

## Features

- Addition of two numbers
- Subtraction of two numbers
- Multiplication of two numbers
- Division of two numbers (with zero-division protection)

## Installation

### Install from PyPI
```bash
pip install mathcalculator
```

### Install from Source
```bash
git clone https://github.com/bracken576/mathcalculator.git
cd mathcalculator
pip install .
```

## Usage

```python
from mathcalculator import MathCalculator

# Initialize the calculator
calc = MathCalculator()

# Perform operations
result_add = calc.add(5, 3)        # Returns 8
result_sub = calc.subtract(10, 4)  # Returns 6
result_mul = calc.multiply(3, 7)   # Returns 21
result_div = calc.divide(15, 3)    # Returns 5.0

print(f"Addition: {result_add}")
print(f"Subtraction: {result_sub}")
print(f"Multiplication: {result_mul}")
print(f"Division: {result_div}")
```

### 6. Additional Files

**.gitignore:**
```
*.pyc
__pycache__/
dist/
build/
*.egg-info/
.pytest_cache/
```

**LICENSE** (MIT License example):
```
MIT License

Copyright (c) 2025 Bracken

Permission is hereby granted, free of charge, to any person obtaining a copy...
```

## Building and Testing the Package

### Local Installation and Testing

1. **Install the package locally:**
   ```bash
   pip install .
   ```

2. **Install with development dependencies:**
   ```bash
   pip install -e .[dev]
   ```

3. **Run tests:**
   ```bash
   pytest tests/
   ```

### Building for Distribution

1. **Install build tools:**
   ```bash
   pip install build twine
   ```

2. **Build the package:**
   ```bash
   python -m build
   ```
   This creates `dist/` folder with `.tar.gz` and `.whl` files.

3. **Check the build:**
   ```bash
   twine check dist/*
   ```

## Publishing to PyPI

### Test PyPI (Recommended first)

1. **Upload to Test PyPI:**
   ```bash
   twine upload --repository testpypi dist/*
   ```

2. **Install from Test PyPI:**
   ```bash
   pip install --index-url https://test.pypi.org/simple/ mathcalculator
   ```

### Production PyPI

```bash
twine upload dist/*
```

## Usage After Installation

Once installed, you can use the package in your Python code:

```python
from mathcalculator import MathCalculator

# Create calculator instance
calc = MathCalculator()

# Perform calculations
print(f"5 + 3 = {calc.add(5, 3)}")
print(f"10 - 4 = {calc.subtract(10, 4)}")
print(f"6 * 7 = {calc.multiply(6, 7)}")
print(f"20 / 4 = {calc.divide(20, 4)}")
```

## Best Practices

1. **Dependencies**: Keep dependencies minimal and specify version ranges
2. **Testing**: Write comprehensive tests before publishing
3. **Documentation**: Include clear README with usage examples
4. **Licensing**: Always include a license file
5. **Git Integration**: Use `.gitignore` to exclude build artifacts

## Conclusion
I used to think that publishing a package would be the main issue; however, it is really simple to use `pyproject.toml`. This allows writing the code to be the main difficulty, which is how it should be. Also, it's very important to write tests and look for edge cases in your code when you are publishing a package since you never know how someone is going to use it. 
