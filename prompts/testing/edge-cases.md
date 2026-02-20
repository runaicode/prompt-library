# Edge Cases

> Identify boundary conditions, unusual inputs, and scenarios that break assumptions.

## When to Use

- You've written tests for happy paths but want thorough coverage
- Preparing for a code review and want to preempt "what about...?" questions
- Working with user input, financial calculations, or safety-critical code
- Fixing a bug and want to make sure related edge cases are covered

## The Prompt

```
Analyze this {{language}} code and identify all edge cases that should be tested.

**Code:**
{{paste_code}}

For each edge case:
1. Describe the scenario
2. Explain what could go wrong (the risk)
3. Provide a concrete test input
4. Show the expected behavior (pass, throw, return specific value)
5. Rate severity: 🔴 crash/data loss, 🟡 wrong result, 🟢 cosmetic

Group by category:
- **Boundary values** (min, max, zero, empty, one)
- **Type issues** (null, undefined, wrong type, NaN, Infinity)
- **Concurrency** (race conditions, deadlocks, stale data)
- **Environment** (disk full, network down, permissions)
- **Business logic** (impossible states, conflicting rules)

Then generate the actual test code for the top 10 most critical edge cases.
```

## Example

### Input

```python
def paginate(items: list, page: int, per_page: int) -> dict:
    total = len(items)
    total_pages = (total + per_page - 1) // per_page
    start = (page - 1) * per_page
    end = start + per_page
    
    return {
        'items': items[start:end],
        'page': page,
        'per_page': per_page,
        'total': total,
        'total_pages': total_pages,
        'has_next': page < total_pages,
        'has_prev': page > 1
    }
```

### Expected Output

The AI should identify:

- `per_page = 0` (ZeroDivisionError)
- `per_page < 0` (negative step, wrong results)
- `page = 0` or `page < 0` (negative index, unexpected slice behavior)
- `page > total_pages` (empty items but wrong metadata)
- Empty list (total_pages = 0, page 1 still valid?)
- Single item, single page
- Exact page boundary (e.g., 10 items, per_page=5, page=2)
- Very large values (page=999999, per_page=1000000)
- Non-integer inputs (float, string) if no type enforcement

## Tips

- **Focus on boundaries**: off-by-one errors are the most common bugs
- Pay special attention to division, modulo, and index operations
- Think about what happens at 0, 1, MAX_INT, and -1
- Consider what happens when two edge cases combine (e.g., empty list AND page 0)
- For financial code, always test floating-point precision (0.1 + 0.2 != 0.3)

## Variations

**For API endpoints:**
```
Add: "Also consider: empty request body, missing required fields, extra unknown fields, 
very long strings (10KB+), Unicode/emoji in text fields, SQL injection attempts, 
and duplicate requests (idempotency)."
```

**For file operations:**
```
Add: "Consider: file doesn't exist, file is empty, file is very large (>1GB), 
file is being written to by another process, symbolic links, 
special characters in filename, and permission denied."
```
