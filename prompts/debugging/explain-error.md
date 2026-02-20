# Explain Error

> Get a clear, actionable explanation of error messages and stack traces.

## When to Use

- You encounter an unfamiliar error message
- A stack trace is long and you need to find the relevant frames
- You want to understand not just what went wrong, but why

## The Prompt

```
Explain this error in plain English. I'm working on a {{language/framework}} project.

**Error message:**
{{paste_error_message}}

**Stack trace (if available):**
{{paste_stack_trace}}

**Code that triggered it:**
{{paste_relevant_code}}

Please provide:
1. A plain-English explanation of what this error means
2. The specific line in MY code (not library code) that caused it
3. The most likely root cause
4. A concrete fix with code
5. What to search for if this fix doesn't work
```

## Example

### Input

```
Explain this error in plain English. I'm working on a Python/FastAPI project.

**Error message:**
pydantic.error_wrappers.ValidationError: 1 validation error for UserCreate
email
  value is not a valid email address (type=value_error.email)

**Code that triggered it:**
class UserCreate(BaseModel):
    name: str
    email: EmailStr
    age: int

@app.post("/users")
async def create_user(user: UserCreate):
    return {"id": 1, **user.dict()}

# Called with: POST /users {"name": "John", "email": "john@", "age": 30}
```

### Expected Output

The AI should explain that Pydantic's `EmailStr` type validates email format, and "john@" is missing the domain part. It should suggest either fixing the input data, adding a custom validator with a better error message, or handling the `ValidationError` with a try/except to return a 422 with a user-friendly message.

## Tips

- **Always include the full error message** — don't truncate it
- Include 2-3 stack frames of YOUR code, not the entire trace
- Mention what you were trying to do when the error occurred
- If it's an intermittent error, mention how often it occurs

## Variations

**For compile/build errors:**
```
Add: "This is a compile-time / build error. Include the build command you ran 
and your build configuration (tsconfig.json, Cargo.toml, etc.)."
```

**For production-only errors:**
```
Add: "This error only occurs in production, not locally. 
My production environment is {{describe environment}}."
```

**For multiple related errors:**
```
Add: "I'm seeing multiple errors. Please identify which one is the ROOT error 
and which are cascading failures."
```
