# Prompt Library for AI Coding

A collection of tested, production-ready prompts for AI coding assistants. Each prompt is designed to get consistent, high-quality results from tools like Claude Code, GitHub Copilot, ChatGPT, and others.

## Why This Exists

Most developers use AI coding tools with ad-hoc prompts and get inconsistent results. This library provides **structured prompt templates** that have been tested across multiple LLMs and refined based on real-world usage.

## Categories

### Debugging
- **[Find Bug](prompts/debugging/find-bug.md)** — Systematically locate the root cause of a bug
- **[Explain Error](prompts/debugging/explain-error.md)** — Get clear explanations of error messages and stack traces

### Refactoring
- **[Extract Function](prompts/refactoring/extract-function.md)** — Break down large functions into smaller, focused units
- **[Modernize Code](prompts/refactoring/modernize-code.md)** — Update legacy code to use modern language features

### Testing
- **[Generate Unit Tests](prompts/testing/generate-unit-tests.md)** — Create comprehensive test suites with edge cases
- **[Edge Cases](prompts/testing/edge-cases.md)** — Identify and test boundary conditions and unusual inputs

### Documentation
- **[Add Docstrings](prompts/documentation/add-docstrings.md)** — Generate clear, consistent documentation for functions and classes

### Code Review
- **[Security Review](prompts/code-review/security-review.md)** — Identify security vulnerabilities and suggest fixes

## How to Use These Prompts

1. **Copy the template** from the relevant `.md` file
2. **Fill in the placeholders** (marked with `{{brackets}}`)
3. **Paste into your AI coding tool** (Claude Code, Copilot Chat, ChatGPT, etc.)
4. **Review the output** and iterate if needed

### Tips for Best Results

- **Provide context**: Include relevant code, file paths, and project structure
- **Be specific**: "Fix the null pointer in the user auth flow" > "Fix my code"
- **Iterate**: Use the AI's response as input for follow-up prompts
- **Verify**: Always review and test AI-generated code before merging

## Prompt Anatomy

Each prompt file follows this structure:

```
# Prompt Title
> One-line description

## When to Use
Scenarios where this prompt works best

## The Prompt
The actual template with {{placeholders}}

## Example
Real input/output demonstration

## Tips
How to get the best results

## Variations
Alternative versions for different contexts
```

## Contributing

Have a prompt that works well? Submit a PR! Include:

1. The prompt template with clear placeholders
2. At least one example with input and output
3. Tips based on your experience using it

## License

MIT — use these prompts however you like.
