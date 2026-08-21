---
title: Engineering Principles
parent: Engineering
grand_parent: Misc
nav_order: 10
layout: default
---
# Table of Contents 
{:.no_toc}

1. TOC 
{:toc}

--- 
# Software Engineering Principles

A collection of practical principles for designing, implementing, testing, and maintaining software.

## Core Design Principles

### 1. Single Responsibility Principle (SRP)

A component should have one clear responsibility and one primary reason to change.

### 2. Separation of Concerns

Keep unrelated responsibilities isolated:

- Business logic
- I/O
- Persistence
- Presentation
- Configuration
- Networking

### 3. High Cohesion, Low Coupling

**High cohesion:** Things that belong together should stay together.

**Low coupling:** Components should depend on each other as little as practical.

### 4. KISS — Keep It Simple

Prefer the simplest design that correctly solves the problem.

Complexity should have a concrete justification.

### 5. YAGNI — You Aren't Gonna Need It

Don't implement speculative functionality before there is an actual requirement for it.

### 6. DRY — Don't Repeat Yourself

Avoid duplicating knowledge or logic.

Do not blindly eliminate every repeated line of code if doing so makes the design more complicated.

### 7. Composition Over Inheritance

Prefer assembling behavior from smaller components rather than creating deep inheritance hierarchies.

### 8. Program to Interfaces

Depend on the behavior a component provides rather than its specific implementation.

---

## Unix and Systems-Oriented Principles

### 9. Do One Thing Well

A program, function, or module should have a focused purpose.

### 10. Small, Composable Components

Build components that can be combined rather than one enormous system that does everything.

### 11. Use Universal Interfaces

Prefer well-defined interfaces such as:

- Files
- Streams
- HTTP
- JSON
- stdin/stdout
- Standard data structures

### 12. Make Components Replaceable

Design components so an implementation can be swapped without rewriting the rest of the system.

### 13. Fail Clearly

Errors should be:

- Explicit
- Observable
- Actionable

Do not silently turn failures into misleading success.

### 14. Make the Right Thing Easy

APIs and abstractions should naturally encourage correct usage.

---

## Reliability Principles

### 15. Defensive Programming

Assume inputs, dependencies, networks, files, and external services can fail.

### 16. Validate at Boundaries

Validate external or untrusted data when it enters your system rather than spreading validation logic throughout the application.

### 17. Explicit State

Make important state visible rather than hiding it in:

- Implicit globals
- Side effects
- Magic behavior
- Hidden dependencies

### 18. Idempotency

Whenever practical, make an operation safe to execute multiple times.

For example:

```python
create_user()
```

is harder to retry safely than:

```python
ensure_user_exists()
```

### 19. Least Privilege

Give processes, services, users, and components only the permissions they actually need.

### 20. Observability

Systems should make it possible to determine what happened through appropriate:

- Logs
- Metrics
- Traces
- Error information

---

## Maintainability Principles

### 21. Readability Over Cleverness

Code is primarily written for humans and secondarily executed by machines.

Prefer obvious code over clever code.

### 22. Explicit Over Implicit

Prefer obvious dependencies and behavior over magic.

### 23. Local Reasoning

You should be able to understand what a function does without examining the entire application.

### 24. Minimize Hidden Side Effects

A function should not unexpectedly modify unrelated:

- State
- Files
- Databases
- Global variables
- External systems

### 25. Stable Interfaces

Internal implementations should be changeable without forcing unrelated consumers to change.

### 26. Automate Repetition

If humans repeatedly perform a deterministic task, consider turning it into tooling or automation.

### 27. Make Changes Reversible

Especially in production systems, prefer changes that can be:

- Rolled back
- Disabled
- Safely retried

---

## Testing Principles

### 28. Test Behavior, Not Implementation

Tests should primarily verify what the software does rather than how it happens to be implemented.

### 29. Fast Feedback

The shorter the time between making a change and discovering that it broke something, the better.

### 30. Test at Appropriate Boundaries

Use an appropriate combination of:

- Unit tests
- Integration tests
- End-to-end tests

Do not rely exclusively on one type.

### 31. Reproduce Before Fixing

When practical, use the following workflow:

```text
Bug
 ↓
Reproduction
 ↓
Test
 ↓
Fix
```

This prevents fixing only the symptom while leaving the underlying behavior unprotected.

---

## Python Application

These principles map particularly well to Python through:

- Small, focused functions
- Modules with clear responsibilities
- Composition
- Explicit data structures
- Dependency injection
- Clear interfaces
- Exceptions for exceptional conditions
- Minimal global state
- Small, testable units
- Explicit configuration
- Predictable side effects

For example, instead of creating one large function that retrieves data, validates it, transforms it, writes it to a database, and prints the result, separate those responsibilities:

```python
def fetch_data():
    ...


def validate_data(data):
    ...


def transform_data(data):
    ...


def save_data(data):
    ...


def main():
    data = fetch_data()
    validate_data(data)
    data = transform_data(data)
    save_data(data)
```

Each component has a smaller responsibility and can be understood, tested, and replaced independently.

---

## Overarching Principle

A large portion of good software engineering can be reduced to four goals:

1. **Reduce unnecessary complexity**
2. **Isolate change**
3. **Make behavior explicit**
4. **Build small components with well-defined interfaces**

Good engineering is not about following every principle rigidly. The principles are tools for reasoning about trade-offs and preventing unnecessary complexity.
