# Add Docstrings

> Generate clear, consistent documentation for functions, classes, and modules.

## When to Use

- Code has no documentation and you need to add it quickly
- Onboarding new team members who need to understand the codebase
- Preparing code for an open-source release
- IDE tooltips and auto-complete need better descriptions

## The Prompt

```
Add documentation to the following {{language}} code.

**Documentation style**: {{style, e.g., Google, NumPy, JSDoc, Javadoc, XML doc comments}}

**Code:**
{{paste_code}}

Requirements:
1. Add a docstring/doc comment to every public function, method, and class
2. Document:
   - What the function does (one-line summary)
   - Parameters with types and descriptions
   - Return value with type and description
   - Exceptions/errors that can be raised
   - Usage example for non-obvious functions
3. Keep descriptions concise — explain WHAT and WHY, not HOW
4. Don't state the obvious (avoid "This function gets the user" for `getUser`)
5. Document side effects (database writes, file changes, network calls)
6. Add module/file-level docstring explaining the overall purpose
7. Include type hints/annotations if the language supports them
```

## Example

### Input

```python
class RateLimiter:
    def __init__(self, max_requests, window_seconds):
        self.max_requests = max_requests
        self.window = window_seconds
        self.requests = {}
    
    def allow(self, client_id):
        now = time.time()
        if client_id not in self.requests:
            self.requests[client_id] = []
        
        self.requests[client_id] = [
            t for t in self.requests[client_id] 
            if now - t < self.window
        ]
        
        if len(self.requests[client_id]) >= self.max_requests:
            return False
        
        self.requests[client_id].append(now)
        return True
    
    def remaining(self, client_id):
        now = time.time()
        if client_id not in self.requests:
            return self.max_requests
        recent = [t for t in self.requests[client_id] if now - t < self.window]
        return max(0, self.max_requests - len(recent))
    
    def reset(self, client_id):
        self.requests.pop(client_id, None)
```

### Expected Output

The AI should add Google-style Python docstrings:

```python
"""Sliding window rate limiter for controlling request frequency per client."""

class RateLimiter:
    """Sliding window rate limiter that tracks requests per client.
    
    Uses an in-memory dictionary to store request timestamps. Not suitable
    for distributed systems — use Redis-backed implementation instead.
    
    Example:
        limiter = RateLimiter(max_requests=100, window_seconds=60)
        if limiter.allow("user_123"):
            process_request()
        else:
            return HttpResponse(status=429)
    """
```

...and so on for each method.

## Tips

- **Specify the doc style** upfront — mixing styles looks unprofessional
- For Python: Google style is most readable; NumPy style for scientific code
- For JavaScript/TypeScript: JSDoc with `@param`, `@returns`, `@throws`
- Ask the AI to flag any functions that are too complex to document clearly (sign they need refactoring)
- Include existing documentation so the AI can match the tone and level of detail

## Variations

**For TypeScript/JavaScript:**
```
"Use JSDoc format with @param, @returns, @throws, and @example tags. 
Include type annotations in JSDoc even if TypeScript types exist."
```

**For API documentation:**
```
"Also add OpenAPI/Swagger annotations or comments that describe 
request/response schemas, status codes, and authentication requirements."
```

**For internal/private code:**
```
"Focus on the WHY — these are internal functions that other team members 
need to understand for maintenance. Explain design decisions and trade-offs."
```
