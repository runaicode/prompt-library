# Generate Unit Tests

> Create comprehensive test suites with proper coverage of happy paths, edge cases, and error conditions.

## When to Use

- Writing tests for existing code that lacks coverage
- Starting TDD and need initial test structure
- Adding tests before refactoring to ensure nothing breaks
- Need to quickly cover a module before a deadline

## The Prompt

```
Generate comprehensive unit tests for the following {{language}} code.

**Testing framework**: {{framework, e.g., pytest, Jest, JUnit}}
**Code to test:**
{{paste_code}}

Requirements:
1. Test all public functions/methods
2. Cover these scenarios for each function:
   - Happy path (normal expected usage)
   - Edge cases (empty inputs, boundary values, null/undefined)
   - Error conditions (invalid inputs, exceptions)
   - Type coercion or implicit conversion issues
3. Use descriptive test names that explain the scenario being tested
4. Group related tests logically (by function or by behavior)
5. Use proper setup/teardown for shared state
6. Mock external dependencies (database, API calls, file system)
7. Include comments explaining WHY each edge case matters
```

## Example

### Input

```python
# Testing framework: pytest

class ShoppingCart:
    def __init__(self):
        self.items = []
    
    def add_item(self, name: str, price: float, quantity: int = 1):
        if price < 0:
            raise ValueError("Price cannot be negative")
        if quantity < 1:
            raise ValueError("Quantity must be at least 1")
        
        for item in self.items:
            if item['name'] == name:
                item['quantity'] += quantity
                return
        
        self.items.append({'name': name, 'price': price, 'quantity': quantity})
    
    def remove_item(self, name: str):
        self.items = [item for item in self.items if item['name'] != name]
    
    def get_total(self, discount_percent: float = 0):
        subtotal = sum(item['price'] * item['quantity'] for item in self.items)
        return round(subtotal * (1 - discount_percent / 100), 2)
    
    def item_count(self):
        return sum(item['quantity'] for item in self.items)
```

### Expected Output

The AI should generate ~20-25 test functions covering:

- `add_item`: normal add, duplicate name (quantity merge), negative price, zero quantity, float precision
- `remove_item`: existing item, non-existent item (no error), empty cart
- `get_total`: no items, single item, multiple items, with discount, 100% discount, float rounding
- `item_count`: empty cart, one item, multiple items, items with quantity > 1

## Tips

- **Include the types/interfaces** — helps the AI generate type-correct test data
- Mention any existing test patterns in your codebase for consistency
- If using dependency injection, include the interfaces being mocked
- Ask for test data factories/fixtures if you'll add more tests later
- Specify coverage target if relevant (e.g., "aim for 90%+ branch coverage")

## Variations

**For API endpoints:**
```
Add: "These are API endpoint tests. Use test client to make HTTP requests. 
Test status codes, response body structure, headers, and authentication."
```

**For React components:**
```
Add: "Use React Testing Library. Test rendering, user interactions, 
state changes, and accessibility. Don't test implementation details."
```

**For database operations:**
```
Add: "Use an in-memory database or transaction rollback for isolation. 
Test CRUD operations, constraints, and concurrent access."
```
