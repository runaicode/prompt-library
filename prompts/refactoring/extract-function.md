# Extract Function

> Break down large, complex functions into smaller, focused, testable units.

## When to Use

- A function is longer than ~30 lines
- A function does more than one thing
- You see repeated patterns that could be abstracted
- Unit testing is difficult because of tangled logic

## The Prompt

```
Refactor the following function by extracting smaller, focused functions. 

**Language**: {{language}}
**Current function:**
{{paste_function}}

Requirements:
1. Each extracted function should do ONE thing and have a descriptive name
2. Preserve the original behavior exactly (no logic changes)
3. Make the extracted functions independently testable
4. Keep the original function as a high-level orchestrator
5. Add brief docstrings/comments explaining each extracted function's purpose
6. Show me the before/after so I can verify nothing changed
```

## Example

### Input

```python
def process_order(order_data):
    # Validate
    if not order_data.get('items'):
        raise ValueError("No items")
    if not order_data.get('customer_id'):
        raise ValueError("No customer")
    for item in order_data['items']:
        if item['quantity'] <= 0:
            raise ValueError(f"Invalid quantity for {item['name']}")
    
    # Calculate totals
    subtotal = 0
    for item in order_data['items']:
        price = item['price'] * item['quantity']
        if item.get('discount'):
            price *= (1 - item['discount'] / 100)
        subtotal += price
    tax = subtotal * 0.08
    shipping = 5.99 if subtotal < 50 else 0
    total = subtotal + tax + shipping
    
    # Save to database
    order = db.orders.insert({
        'customer_id': order_data['customer_id'],
        'items': order_data['items'],
        'subtotal': subtotal,
        'tax': tax,
        'shipping': shipping,
        'total': total,
        'status': 'pending'
    })
    
    # Send confirmation
    customer = db.customers.find(order_data['customer_id'])
    send_email(customer['email'], 'order_confirmation', {'order_id': order['id'], 'total': total})
    
    return order
```

### Expected Output

The AI should extract: `validate_order()`, `calculate_item_price()`, `calculate_order_totals()`, `save_order()`, and `send_order_confirmation()`, keeping `process_order()` as a clean orchestrator.

## Tips

- If you have tests for the original function, include them so the AI can verify behavior is preserved
- Mention your project's naming conventions (camelCase, snake_case, etc.)
- If some pieces are reused elsewhere, mention it — the AI may suggest shared utility functions
- Ask for the functions in dependency order if you care about file organization

## Variations

**For class methods:**
```
Add: "Some of these extracted functions should be private methods on the class. 
Others might belong in a separate utility module. Recommend which is which."
```

**For performance-sensitive code:**
```
Add: "This is in a hot path. Don't introduce function call overhead 
where it matters — inline small operations."
```
