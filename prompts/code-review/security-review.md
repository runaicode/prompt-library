# Security Review

> Identify security vulnerabilities, common attack vectors, and suggest hardened alternatives.

## When to Use

- Before merging code that handles user input, authentication, or sensitive data
- Reviewing code that interacts with databases, file systems, or external APIs
- Auditing an existing codebase for security issues
- Preparing for a security assessment or penetration test

## The Prompt

```
Perform a security review of the following {{language}} code.

**Context**: {{what the code does, e.g., "user registration API endpoint"}}
**Framework**: {{framework/stack}}
**Deployment**: {{environment, e.g., "public-facing web app behind Cloudflare"}}

**Code:**
{{paste_code}}

Review for:
1. **Injection** — SQL injection, command injection, XSS, template injection
2. **Authentication/Authorization** — broken auth, privilege escalation, session issues
3. **Data Exposure** — sensitive data in logs, responses, or error messages
4. **Input Validation** — missing validation, type coercion, buffer overflow
5. **Cryptography** — weak algorithms, hardcoded secrets, improper key management
6. **Dependencies** — known vulnerable packages, outdated libraries
7. **Configuration** — debug mode, verbose errors, permissive CORS
8. **Business Logic** — race conditions, IDOR, mass assignment

For each finding:
- **Severity**: Critical / High / Medium / Low / Informational
- **Location**: specific line(s) of code
- **Issue**: what's wrong
- **Impact**: what an attacker could do
- **Fix**: concrete code change (not just "sanitize input")
- **Reference**: relevant CWE or OWASP category
```

## Example

### Input

```javascript
// Context: User login endpoint
// Framework: Express.js + MongoDB

app.post('/api/login', async (req, res) => {
  const { username, password } = req.body;
  
  const user = await db.collection('users').findOne({ 
    username: username,
    password: password 
  });
  
  if (user) {
    const token = jwt.sign({ id: user._id, role: user.role }, 'mysecretkey');
    res.json({ token, message: `Welcome ${username}` });
  } else {
    res.status(401).json({ error: `Invalid credentials for ${username}` });
  }
});
```

### Expected Output

The AI should identify at minimum:

1. **Critical: NoSQL Injection** — `username` and `password` from `req.body` go directly into the MongoDB query without validation. Attacker can send `{"username": {"$gt": ""}, "password": {"$gt": ""}}` to bypass auth. **Fix**: validate types with `typeof username === 'string'` or use a schema validator.

2. **Critical: Plaintext Password Storage** — comparing password directly means passwords are stored in plaintext. **Fix**: use bcrypt to hash passwords and `bcrypt.compare()` for verification.

3. **High: Hardcoded JWT Secret** — `'mysecretkey'` is a weak, hardcoded secret. **Fix**: use `process.env.JWT_SECRET` with a strong random value.

4. **Medium: Username Enumeration** — error message reveals whether the username exists. **Fix**: use generic "Invalid credentials" message.

5. **Medium: No Rate Limiting** — endpoint is vulnerable to brute force. **Fix**: add `express-rate-limit` middleware.

6. **Low: Missing JWT Expiration** — tokens never expire. **Fix**: add `{ expiresIn: '1h' }` to `jwt.sign()`.

## Tips

- **Include ALL related code** — middleware, config files, environment setup
- Mention what security measures are already in place (WAF, rate limiting, etc.)
- If this is an API, include the route definitions and middleware chain
- For frontend code, mention your CSP headers and CORS configuration
- Ask specifically about OWASP Top 10 categories if you want comprehensive coverage

## Variations

**For infrastructure code (Terraform, CloudFormation):**
```
"Review for: overly permissive IAM policies, public S3 buckets, unencrypted resources, 
missing logging/monitoring, security group misconfigurations, and hardcoded credentials."
```

**For mobile apps:**
```
"Also check for: insecure local storage, certificate pinning, 
sensitive data in logs/screenshots, and reverse engineering protections."
```

**For CI/CD pipelines:**
```
"Review for: secrets in environment variables, supply chain attacks, 
unsigned artifacts, and excessive permissions in workflow files."
```
