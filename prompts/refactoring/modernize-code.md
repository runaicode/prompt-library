# Modernize Code

> Update legacy code to use modern language features while preserving behavior.

## When to Use

- Codebase uses outdated patterns (callbacks instead of async/await, var instead of const, etc.)
- Upgrading language/framework version and want to use new features
- Code review feedback says "use modern syntax"
- Preparing code for a major version upgrade

## The Prompt

```
Modernize the following {{language}} code to use current best practices and language features.

**Current version**: {{current_language_version}}
**Target version**: {{target_language_version}}

**Code to modernize:**
{{paste_code}}

Requirements:
1. Use modern language features (list specific ones you want, e.g., async/await, destructuring, optional chaining)
2. Preserve ALL existing behavior — this is a refactor, not a rewrite
3. Improve readability and maintainability
4. Show each change with a brief comment explaining the modernization
5. Flag any behavioral differences introduced by the modernization (even subtle ones)
6. Note any deprecated APIs that should be replaced
```

## Example

### Input

```javascript
// Current: Node.js 12, Target: Node.js 20

var request = require('request');
var fs = require('fs');

function getUser(id, callback) {
  request('https://api.example.com/users/' + id, function(err, response, body) {
    if (err) {
      callback(err, null);
      return;
    }
    var user = JSON.parse(body);
    if (user.avatar) {
      request(user.avatar, function(err2, response2, avatarData) {
        if (err2) {
          callback(null, Object.assign({}, user, { avatar: null }));
        } else {
          callback(null, Object.assign({}, user, { avatarData: avatarData }));
        }
      });
    } else {
      callback(null, user);
    }
  });
}

function processUsers(ids) {
  var results = [];
  var completed = 0;
  ids.forEach(function(id) {
    getUser(id, function(err, user) {
      if (!err) results.push(user);
      completed++;
      if (completed === ids.length) {
        console.log('Done:', results.length, 'users');
      }
    });
  });
}
```

### Expected Output

The AI should convert to: `const`/`let`, arrow functions, template literals, `async/await` with `fetch`, `Promise.allSettled()`, destructuring, optional chaining, and `import` syntax. It should note that `fetch` behaves slightly differently from `request` (no auto-parse, different error handling).

## Tips

- **Be explicit about the target version** — "modern" means different things for Node 18 vs Node 22
- Mention if you're also changing frameworks (e.g., Express to Fastify)
- If you have a linter config (.eslintrc), include it so the AI matches your rules
- Ask for the changes in stages if the file is large — modernize one pattern at a time

## Variations

**For Python 2 to 3:**
```
Add: "Pay special attention to: print statements, unicode/str handling, 
dict.keys()/values()/items(), integer division, and exception syntax."
```

**For Java:**
```
Add: "Use records, sealed classes, pattern matching, text blocks, 
and var where appropriate. Target Java 21."
```

**For TypeScript migration:**
```
Change prompt to: "Convert this JavaScript to TypeScript. Add proper type annotations 
(no 'any' types). Create interfaces for data structures. Use strict mode."
```
