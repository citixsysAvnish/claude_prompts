# AI Prompt Templates for Code Optimization

To get the most out of an AI when optimizing your application code, be specific about what you want to improve — such as speed, memory usage, readability, or security.

Below are optimized prompt templates based on common software development needs.

---

# 1. Performance & Execution Speed

Use this when your code is slow or contains inefficient loops and operations.

## Prompt

```text
Act as a performance engineer. Profile the following code mentally and suggest optimizations to improve execution speed and reduce time complexity (e.g., from O(n²) to O(n log n)). Point out expensive operations and provide the optimized version.

Code:
[paste code]
````

---

# 2. Memory Efficiency

Use this when working with large datasets or memory-constrained environments.

## Prompt

```text
Review this project for memory efficiency. Identify any potential memory leaks, redundant object creation, or large data structures that could be optimized (e.g., using generators or more efficient data types). Suggest a version that minimizes the memory footprint.

Code:
[paste code]
```

---

# 3. Readability & Maintainability (Refactoring)

Use this for difficult-to-maintain or poorly structured code.

## Prompt

```text
Refactor this project to improve readability and maintainability without changing its external behavior. Apply SOLID principles, extract magic numbers into constants, and use descriptive variable names. Explain each significant change.

Code:
[paste code]
```

---

# 4. Security & Vulnerability Audit

Use this to identify common security vulnerabilities such as SQL Injection, XSS, or insecure authentication flows.

## Prompt

```text
Act as a security researcher. Audit the following code for vulnerabilities such as SQL injection, XSS, or insecure authentication patterns. List the issues found, explain the risks, and provide secure code fixes.

Code:
[paste code]
```

---

# 5. Comprehensive Code Review

Use this for an overall assessment of code quality.

## Prompt

```text
Act as a senior software engineer. Review this code for best practices, performance, security, and maintainability. For each suggestion, explain the trade-offs. Finally, give the code a quality rating from 1 to 10 with a brief justification.

Language/Framework:
[e.g., React/TypeScript]

Code:
[paste code]
```

---

# Tips for Better Results

## Give Context

Always mention:

* Programming language
* Framework
* Runtime or platform

Example:

```text
Language: C#
Framework: .NET 8
Environment: Windows Desktop Application
```

---

## Specify Constraints

Mention any technical limitations or requirements.

Examples:

```text
- Must run in under 200ms
- Must support 1 million records
- Must work in browser environment
- Must avoid third-party libraries
```

---

## Iterate with Follow-up Questions

If the first result is not ideal, continue refining.

Examples:

```text
- Can you make this more concise?
- Can you reduce allocations further?
- How would this handle null inputs?
- Can this be parallelized safely?
- What edge cases are missing?
```

---

# Recommended Workflow

1. Start with a general code review.
2. Optimize performance bottlenecks.
3. Reduce memory usage.
4. Refactor for maintainability.
5. Perform a security audit.
6. Benchmark and test edge cases.

---

# Example Combined Prompt

```text
Act as a senior software engineer and performance expert. Review the following C# code for performance, memory usage, security, and maintainability. Suggest optimizations with explanations and provide a refactored version. Highlight any risks or trade-offs.

Constraints:
- Must run under 100ms
- Must support large datasets
- Must remain compatible with .NET Framework 4.8

Code:
[paste code]
```

```
```
