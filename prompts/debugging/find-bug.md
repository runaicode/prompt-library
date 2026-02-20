# Find Bug

> Systematically locate the root cause of a bug using structured analysis.

## When to Use

- You have a bug you can reproduce but can't find the cause
- Stack traces aren't pointing to the real issue
- The bug involves multiple files or complex data flow

## The Prompt

```
I have a bug with the following behavior:

**Expected**: {{expected_behavior}}
**Actual**: {{actual_behavior}}
**Steps to reproduce**: {{reproduction_steps}}

Here's the relevant code:

{{paste_code_here}}

Please:
1. Trace the data flow from input to the point of failure
2. Identify the exact line(s) where behavior diverges from expectations
3. Explain WHY the bug occurs (root cause, not just symptoms)
4. Provide a minimal fix with explanation
5. Suggest how to prevent similar bugs (e.g., type checks, assertions, tests)
```

## Example

### Input

```
I have a bug with the following behavior:

**Expected**: User sees "Welcome, John" after login
**Actual**: User sees "Welcome, undefined" after login
**Steps to reproduce**: Login with valid credentials, observe the welcome message

Here's the relevant code:

// auth.js
async function login(email, password) {
  const response = await fetch('/api/login', {
    method: 'POST',
    body: JSON.stringify({ email, password })
  });
  const data = await response.json();
  localStorage.setItem('user', data);
  return data;
}

// dashboard.js
function showWelcome() {
  const user = localStorage.getItem('user');
  document.getElementById('welcome').textContent = `Welcome, ${user.name}`;
}
```

### Output

The AI should identify that `localStorage.setItem('user', data)` stores `[object Object]` because `data` is an object being coerced to a string. Then `localStorage.getItem('user')` returns that string, which has no `.name` property.

**Fix**: `localStorage.setItem('user', JSON.stringify(data))` and `const user = JSON.parse(localStorage.getItem('user'))`.

## Tips

- Include **all** related code, not just the file you think has the bug
- Mention the language, framework version, and runtime environment
- If the bug is intermittent, describe the conditions when it does/doesn't appear
- Include any error messages or console output verbatim

## Variations

**For race conditions:**
```
Add to the prompt: "This bug only appears under concurrent access / high load. 
Please analyze potential race conditions, shared mutable state, and timing issues."
```

**For data bugs:**
```
Add: "Include sample input data that triggers the bug and sample data that works correctly."
```
