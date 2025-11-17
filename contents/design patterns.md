# Design Patterns

A concise reference of common software design patterns grouped by category. Each pattern has a short description and a minimal example or usage note.

---

## Overview

Design patterns are repeatable solutions to common software design problems. They help communicate intent, improve maintainability, and provide proven ways to structure code.

## Creational Patterns

- **Singleton**: Ensure a class has only one instance and provide a global point of access.

  - Use for shared resources (configuration, logging). Prefer dependency injection when possible.
  - Pseudocode:
    ```
    class Singleton {
      private static instance
      static getInstance() {
        if (!instance) instance = new Singleton()
        return instance
      }
    }
    ```

- **Factory Method**: Define an interface for creating an object, letting subclasses decide which class to instantiate.

  - Good when a class defers instantiation to subclasses.

- **Abstract Factory**: Provide an interface to create families of related objects without specifying concrete classes.

  - Useful for theming or platform-specific implementations.

- **Builder**: Separate construction of a complex object from its representation.

  - Use when an object has many optional parameters.

- **Prototype**: Create new objects by copying an existing object (cloning).
  - Helpful when object creation is expensive.

## Structural Patterns

- **Adapter**: Convert the interface of a class into another interface clients expect.

  - Useful for integrating legacy code.

- **Decorator**: Add responsibilities to objects dynamically without subclassing.

  - Prefer over subclassing when you need flexible combinations of behavior.

- **Facade**: Provide a simplified interface to a set of interfaces in a subsystem.

  - Use to create a cleaner API for complex libraries.

- **Proxy**: Provide a surrogate or placeholder for another object to control access.

  - Examples: lazy-loading, access control, remote proxies.

- **Composite**: Compose objects into tree structures to represent part-whole hierarchies.
  - Treat individual objects and compositions uniformly.

## Behavioral Patterns

- **Strategy**: Define a family of algorithms, encapsulate each one, and make them interchangeable.

  - Use to swap algorithms at runtime.
  - Pseudocode:
    ```
    interface Strategy { execute(data) }
    class Context { setStrategy(s); run() { strategy.execute(data) } }
    ```

- **Observer**: Define a one-to-many dependency so that when one object changes state, its dependents are notified.

  - Useful for event systems, UI updates.

- **Command**: Encapsulate a request as an object, allowing parameterization and queuing of requests.

  - Use for undo/redo, task queues.

- **Template Method**: Define the skeleton of an algorithm in an operation, deferring some steps to subclasses.

  - Good for code reuse while allowing customization.

- **Iterator**: Provide a way to access elements of an aggregate sequentially without exposing its underlying representation.

- **State**: Allow an object to alter its behavior when its internal state changes.

- **Mediator**: Define an object that encapsulates how a set of objects interact, reducing direct dependencies.

- **Visitor**: Represent an operation to be performed on elements of an object structure, allowing new operations without modifying element classes.

## When to Use Patterns

- Use patterns as a communication tool and guideline, not a strict rule.
- Prefer simpler solutions first; introduce patterns when they solve real problems (duplication, coupling, complexity).

## Quick Examples & Notes

- Many patterns overlap; e.g., Factory Method and Abstract Factory are related but operate at different scopes.
- In modern codebases prefer composition, interfaces/abstractions, and dependency injection over global singletons.

## Resources

- "Design Patterns: Elements of Reusable Object-Oriented Software" — the Gang of Four (GoF)
- Clean Code / Refactoring books and language-specific pattern guides
- Online pattern catalogs (e.g., Refactoring.guru)

---

If you want, I can add code examples in a specific language (Python, Java, TypeScript), or expand any pattern into a full example.
