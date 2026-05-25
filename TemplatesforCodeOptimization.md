# Advanced Claude Prompt Templates for Code Optimization & Engineering Reviews

These prompts are optimized specifically for Claude models to produce deeper reasoning, structured analysis, cleaner refactoring, and implementation-focused recommendations.

---

# 1. Performance Optimization Prompt

Use this when code execution is slow, CPU-heavy, or algorithmically inefficient.

## Prompt

```text
You are a senior performance engineer and software architect.

Analyze the following code for execution speed, algorithmic efficiency, and scalability.

Your tasks:

1. Identify performance bottlenecks.
2. Explain the current time and space complexity.
3. Detect unnecessary loops, repeated calculations, blocking operations, or excessive allocations.
4. Suggest algorithmic improvements where applicable.
5. Recommend data structure optimizations.
6. Highlight trade-offs between readability and performance.
7. Provide a fully optimized version of the code.
8. Compare the original vs optimized approach.

Output format:

- Performance Issues
- Complexity Analysis
- Optimization Opportunities
- Refactored Code
- Benchmark Expectations
- Trade-offs & Risks

Requirements:
- Preserve existing functionality.
- Prefer clean and maintainable optimizations.
- Avoid premature micro-optimizations unless justified.
- Mention edge cases that could impact performance.

Language/Framework:
[paste language/framework]

Code:
[paste code]
```

---

# 2. Memory Optimization Prompt

Use this for large datasets, high-memory applications, backend services, or browser-heavy apps.

## Prompt

```text
You are a systems engineer specializing in memory optimization and resource efficiency.

Review the following code and identify memory-related inefficiencies.

Focus on:
- Memory leaks
- Unnecessary object allocations
- Large in-memory collections
- Excessive cloning/copying
- Inefficient caching
- Blocking streams/buffers
- Improper disposal of resources
- GC pressure and allocation hotspots

Tasks:
1. Explain memory risks.
2. Identify allocation-heavy patterns.
3. Suggest lower-memory alternatives.
4. Recommend streaming, lazy loading, generators, pooling, or span-based approaches where appropriate.
5. Provide an optimized implementation.
6. Explain trade-offs between memory, readability, and CPU usage.

Output format:
- Memory Risks
- Allocation Analysis
- Optimization Recommendations
- Optimized Code
- Expected Memory Improvements
- Trade-offs

Requirements:
- Maintain readability.
- Preserve business logic.
- Mention any platform-specific considerations.

Language/Framework:
[paste language/framework]

Code:
[paste code]
```

---

# 3. Refactoring & Maintainability Prompt

Use this for legacy systems, technical debt reduction, or code cleanup.

## Prompt

```text
You are a principal software engineer performing a maintainability and architecture review.

Refactor the following code for readability, maintainability, extensibility, and clean architecture.

Tasks:
1. Identify code smells.
2. Detect violations of SOLID, DRY, KISS, or separation of concerns.
3. Improve naming conventions.
4. Extract reusable logic.
5. Remove magic numbers and hardcoded values.
6. Improve error handling.
7. Improve testability.
8. Suggest architectural improvements if necessary.
9. Provide a production-quality refactored version.

Output format:
- Problems Identified
- Design Principle Violations
- Refactoring Strategy
- Refactored Code
- Architectural Recommendations
- Maintainability Improvements

Requirements:
- Do not change external behavior.
- Keep the solution practical and production-ready.
- Explain significant changes clearly.

Language/Framework:
[paste language/framework]

Code:
[paste code]
```

---

# 4. Security Audit Prompt

Use this for APIs, authentication systems, forms, database operations, or user-generated content handling.

## Prompt

```text
You are a senior application security engineer performing a secure code review.

Audit the following code for security vulnerabilities and insecure design patterns.

Check for:
- SQL Injection
- XSS
- CSRF
- SSRF
- Command Injection
- Path Traversal
- Authentication flaws
- Authorization issues
- Insecure deserialization
- Hardcoded secrets
- Sensitive data exposure
- Weak cryptography
- Race conditions
- Dependency risks

Tasks:
1. Identify vulnerabilities.
2. Explain severity and attack scenarios.
3. Provide remediation strategies.
4. Rewrite insecure sections securely.
5. Recommend secure coding best practices.
6. Suggest additional hardening improvements.

Output format:
- Vulnerabilities Found
- Severity Level
- Risk Explanation
- Secure Fixes
- Refactored Secure Code
- Additional Security Recommendations

Requirements:
- Prioritize real-world exploitability.
- Explain why the vulnerability matters.
- Follow OWASP best practices.

Language/Framework:
[paste language/framework]

Code:
[paste code]
```

---

# 5. Comprehensive Engineering Review Prompt

Use this for full production-grade reviews.

## Prompt

```text
You are a staff-level software engineer conducting a production readiness review.

Perform a comprehensive analysis of the following codebase section.

Review areas:
- Code quality
- Architecture
- Maintainability
- Performance
- Scalability
- Security
- Error handling
- Concurrency/thread safety
- Logging and observability
- Testing strategy
- Dependency management

Tasks:
1. Identify weaknesses and risks.
2. Explain technical debt areas.
3. Suggest improvements with rationale.
4. Recommend architectural enhancements where appropriate.
5. Provide refactored examples for critical issues.
6. Rate the code quality from 1–10.
7. Explain the reasoning behind the score.

Output format:
- Executive Summary
- Critical Issues
- Performance Review
- Security Review
- Maintainability Review
- Scalability Concerns
- Refactoring Recommendations
- Suggested Architecture Improvements
- Final Code Quality Score

Requirements:
- Be direct and technically precise.
- Prioritize high-impact improvements first.
- Include trade-offs for major recommendations.

Language/Framework:
[paste language/framework]

Code:
[paste code]
```

---

# 6. Senior Architect Prompt (Advanced)

Use this when designing systems or modernizing enterprise applications.

## Prompt

```text
Act as a senior software architect and enterprise solution consultant.

Review the following implementation from the perspective of:
- Scalability
- Extensibility
- Domain-driven design
- Clean architecture
- Distributed systems readiness
- Performance
- Security
- DevOps readiness
- Observability
- Future maintainability

Tasks:
1. Identify architectural weaknesses.
2. Recommend better design patterns if applicable.
3. Suggest modularization opportunities.
4. Evaluate coupling and cohesion.
5. Recommend improvements for cloud-native environments.
6. Suggest caching, async processing, queues, or event-driven approaches where beneficial.
7. Identify risks that could appear at scale.
8. Provide a revised architecture proposal.

Output format:
- Architecture Review
- Scalability Risks
- Design Pattern Recommendations
- System Design Improvements
- Cloud/DevOps Recommendations
- Future Scaling Considerations
- Revised Architecture Proposal

Language/Framework:
[paste language/framework]

System Context:
[paste business/domain context]

Code:
[paste code]
```

---

# Claude-Specific Best Practices

## 1. Ask for Structured Output

Claude performs better with explicit section formatting.

Example:

```text
Use markdown headings and bullet points for every section.
```

---

## 2. Force Deep Reasoning

Example:

```text
Think step-by-step before proposing optimizations.
Explain why each recommendation matters.
```

---

## 3. Ask for Trade-offs

Example:

```text
For every optimization, explain:
- Benefits
- Risks
- Maintainability impact
- Scalability impact
```

---

## 4. Prevent Overengineering

Example:

```text
Prefer pragmatic production-ready solutions over theoretical optimizations.
```

---

## 5. Ask for Edge Cases

Example:

```text
List edge cases and failure scenarios the implementation should handle.
```

---

# Universal Claude Enhancement Snippet

Append this to almost any engineering prompt:

```text
Before answering:
- Think step-by-step.
- Prioritize practical production-ready improvements.
- Avoid unnecessary complexity.
- Explain trade-offs clearly.
- Mention assumptions explicitly.
- Include edge cases and scalability concerns.
- Use clean markdown formatting.
```
