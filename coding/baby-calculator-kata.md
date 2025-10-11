# Simple Calculator Kata

## What We Value

We value test-driving (using TDD) the solution and growing it in very small, incremental steps while explaining your intentions and design decisions to the interviewer.

For us, it doesn't matter that you build the whole solution during the session, but the way that you go about it and your workflow.

## Problem Statement

Build a simple calculator that performs basic arithmetic operations **without using the built-in multiplication (`*`), division (`/`), or exponentiation (`**`) operators**.

You'll implement the calculator incrementally through a series of user stories.

### Story 1: Addition and Subtraction

**As a** user  
**I want** to add and subtract two numbers  
**So that** I can perform basic arithmetic

**Acceptance Criteria:**
- `add(a, b)` returns the sum of two numbers
- `subtract(a, b)` returns the difference (a - b)
- Works with positive and negative integers
- Works with floating-point numbers

**Examples:**
```
add(2, 3) → 5
add(-1, 5) → 4
subtract(10, 3) → 7
subtract(5, 10) → -5
```

### Story 2: Multiplication

**As a** user  
**I want** to multiply two numbers  
**So that** I can calculate products

**Acceptance Criteria:**
- `multiply(a, b)` returns the product of two numbers
- **Must NOT use the `*` operator** - implement using repeated addition
- Works with positive and negative integers
- Works with zero (e.g., `multiply(5, 0)` → `0`)
- Works with floating-point numbers

**Examples:**
```
multiply(3, 4) → 12
multiply(-2, 5) → -10
multiply(7, 0) → 0
multiply(2.5, 4) → 10.0
```

### Story 3: Division

**As a** user  
**I want** to divide two numbers  
**So that** I can calculate quotients

**Acceptance Criteria:**
- `divide(a, b)` returns the quotient of two numbers
- **Must NOT use the `/` operator** - implement using repeated subtraction
- Works with positive and negative integers
- Works with floating-point numbers
- Should handle division by zero appropriately (raise exception or return error message)

**Examples:**
```
divide(10, 2) → 5.0
divide(15, 4) → 3.75
divide(10, -2) → -5.0
divide(5, 0) → Error: Division by zero
```

### Story 4: Power

**As a** user  
**I want** to raise a number to a power  
**So that** I can calculate exponentials

**Acceptance Criteria:**
- `power(base, exponent)` returns base raised to the exponent
- **Must NOT use the `**` or `pow()` operators** - implement using your multiply function
- Works with positive integer exponents
- Should handle `power(x, 0)` → `1`
- Should handle `power(x, 1)` → `x`

**Examples:**
```
power(2, 3) → 8
power(5, 2) → 25
power(10, 0) → 1
power(3, 1) → 3
```

---

## Assumptions - [Please Don't Give These to the Interviewee Upfront]:

1. **Story 1** is straightforward and can use built-in `+` and `-` operators
2. **Story 2** (Multiply) should be implemented using repeated addition:
   - `3 × 4` means "add 3 to itself 4 times"
   - Need to handle negative numbers (consider sign separately)
   - For floats, can convert to integer multiplication and adjust
3. **Story 3** (Divide) should be implemented using repeated subtraction:
   - `10 ÷ 2` means "how many times can I subtract 2 from 10?"
   - Integer division is easier to start with
   - Floating-point division requires tracking remainders
4. **Story 4** (Power) should be implemented using the multiply function:
   - `2³` means "multiply 2 by itself 3 times"
   - Start with positive integer exponents only
5. Edge cases to consider:
   - Multiplying/dividing by zero
   - Negative numbers
   - Order of operations (not required, just individual operations)
   - Floating-point precision issues

---

## Extension:

Once the basic stories are complete, consider these extensions:

### Extension 1: Negative and Fractional Exponents
- Implement `power(base, exponent)` for negative exponents: `power(2, -3)` → `0.125`
- Implement fractional exponents (square roots): `power(9, 0.5)` → `3.0`

### Extension 2: Chain Operations
- Allow chaining: `Calculator().add(5).multiply(3).subtract(2)` → `13`
- Maintain state between operations

### Extension 3: Expression Parsing
- Accept string expressions: `"5 + 3 * 2"` → `11` (or `16` without precedence)
- Handle operator precedence correctly

### Extension 4: Memory Functions
- Add memory store/recall: `store()`, `recall()`, `clear_memory()`

---

## Facilitation Notes (Warning, Spoilers)

### Common Pitfalls:

1. **Jumping to complex solutions**: Candidates may try to implement all operations at once. Encourage small steps—start with positive integers only, then handle negatives, then floats.

2. **Not handling edge cases**: Watch for:
   - Multiply by zero
   - Division by zero
   - Negative numbers in multiplication (sign handling)
   - Floating-point precision in division

3. **Inefficient algorithms**: Repeated addition/subtraction is intentionally naive. This is fine! The goal is TDD practice, not optimization. However, if they finish early, discuss time complexity (O(n) for multiply, O(n) for divide).

4. **Over-engineering**: Some candidates add unnecessary abstractions early. Guide them to implement the simplest thing that works first.

### Alternative Valid Approaches:

**Multiplication:**
- Repeated addition: `3 × 4 = 3 + 3 + 3 + 3`
- Using bit shifting (advanced): `a × 2 = a << 1`
- Russian peasant multiplication (halving and doubling)

**Division:**
- Repeated subtraction: Keep subtracting divisor until remainder < divisor
- Binary search approach (advanced)
- Handling floats: Multiply by 10^n, perform integer division, adjust decimal

**Power:**
- Repeated multiplication: `2³ = 2 × 2 × 2`
- Divide and conquer (advanced): `2⁸ = (2⁴)²`

### Keeping Discussion Productive:

- **Test names matter**: Encourage descriptive test names like `test_multiply_positive_numbers` rather than `test_multiply`
- **Red-Green-Refactor**: Watch for this cycle. Are they writing the test first? Is it failing for the right reason?
- **Baby steps**: If they write `multiply(a, b)` with full negative number handling immediately, ask: "What's the simplest test you could write first?"
- **Refactoring opportunities**: After Story 2, there might be duplicate sign-handling logic between multiply and the candidate's approach
- **Ask probing questions**:
  - "What edge cases are you thinking about?"
  - "How would you handle negative numbers?"
  - "What would happen if we divide by zero?"

### Example Solution (Python):

```python
class Calculator:
    def add(self, a, b):
        """Add two numbers."""
        return a + b
    
    def subtract(self, a, b):
        """Subtract b from a."""
        return a - b
    
    def multiply(self, a, b):
        """Multiply two numbers using repeated addition."""
        if a == 0 or b == 0:
            return 0
        
        # Handle signs
        negative = (a < 0) ^ (b < 0)  # XOR: result is negative if signs differ
        a, b = abs(a), abs(b)
        
        # For floats, scale up to integers
        if isinstance(a, float) or isinstance(b, float):
            # Simple approach: multiply integer parts and handle decimals
            result = 0
            for _ in range(int(b)):
                result = self.add(result, a)
            # Handle remaining fraction (simplified)
            fraction = b - int(b)
            if fraction > 0:
                result = self.add(result, a * fraction)  # Allowed for fraction
        else:
            result = 0
            for _ in range(b):
                result = self.add(result, a)
        
        return -result if negative else result
    
    def divide(self, a, b):
        """Divide a by b using repeated subtraction."""
        if b == 0:
            raise ValueError("Division by zero")
        
        # Handle signs
        negative = (a < 0) ^ (b < 0)
        a, b = abs(a), abs(b)
        
        # Integer division
        quotient = 0
        remainder = a
        while remainder >= b:
            remainder = self.subtract(remainder, b)
            quotient = self.add(quotient, 1)
        
        # Handle decimal part (simplified - just a few decimal places)
        decimal_part = 0
        decimal_places = 10
        for i in range(1, decimal_places + 1):
            remainder = self.multiply(remainder, 10)
            while remainder >= b:
                remainder = self.subtract(remainder, b)
                decimal_part = self.add(decimal_part, 1 / (10 ** i))
        
        result = self.add(quotient, decimal_part)
        return -result if negative else result
    
    def power(self, base, exponent):
        """Raise base to exponent using repeated multiplication."""
        if exponent == 0:
            return 1
        if exponent == 1:
            return base
        
        result = base
        for _ in range(int(exponent) - 1):
            result = self.multiply(result, base)
        
        return result


# Example test cases
def test_calculator():
    calc = Calculator()
    
    # Story 1: Add and Subtract
    assert calc.add(2, 3) == 5
    assert calc.add(-1, 5) == 4
    assert calc.subtract(10, 3) == 7
    assert calc.subtract(5, 10) == -5
    
    # Story 2: Multiply
    assert calc.multiply(3, 4) == 12
    assert calc.multiply(-2, 5) == -10
    assert calc.multiply(7, 0) == 0
    
    # Story 3: Divide
    assert calc.divide(10, 2) == 5.0
    assert abs(calc.divide(15, 4) - 3.75) < 0.01  # Float comparison
    
    # Story 4: Power
    assert calc.power(2, 3) == 8
    assert calc.power(5, 2) == 25
    assert calc.power(10, 0) == 1
    
    print("All tests passed! ✅")

if __name__ == "__main__":
    test_calculator()
```

### Time Expectations:

- **Story 1**: 5-10 minutes (warm-up)
- **Story 2**: 15-20 minutes (core TDD practice)
- **Story 3**: 15-25 minutes (most complex)
- **Story 4**: 10-15 minutes (builds on Story 2)

**Total**: 45-70 minutes for all stories. Most candidates won't complete everything, and that's expected!

### What Success Looks Like:

- Clear test names and good test structure
- Red-Green-Refactor cycle is visible
- Incremental approach (starts simple, adds complexity)
- Handles edge cases thoughtfully
- Explains design decisions
- Refactors when duplication appears
- Asks clarifying questions about requirements