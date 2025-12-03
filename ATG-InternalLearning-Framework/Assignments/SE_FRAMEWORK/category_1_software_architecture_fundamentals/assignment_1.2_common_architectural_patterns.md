# Assignment 1.2: Common Architectural Patterns

## Assignment Information
- **Category**: Software Architecture Fundamentals
- **Sub-category**: 1.2 Common Architectural Patterns
- **Team Members**: [Pair names to be assigned]
- **Presentation Date**: [To be scheduled]
- **Duration**: 30-45 minutes

---

## Learning Objectives
By the end of this session, the team should be able to:
1. Understand the key differences between MVC, MVVM, MVP, Clean Architecture, Hexagonal, and Onion patterns
2. Select the appropriate architectural pattern based on application requirements and constraints
3. Recognize how these patterns promote testability, maintainability, and separation of concerns

---

## Topics to Cover

### 1. Presentation Patterns (MVC, MVVM, MVP)
- MVC: Model-View-Controller classic pattern
- MVVM: Model-View-ViewModel for rich UI applications
- MVP: Model-View-Presenter for better testability
- When to use each pattern

### 2. Clean Architecture
- Dependency rule: dependencies point inward
- Layers: Entities, Use Cases, Interface Adapters, Frameworks
- Framework independence and testability
- Practical implementation challenges

### 3. Hexagonal Architecture (Ports & Adapters)
- Core business logic at the center
- Ports: interfaces for communication
- Adapters: implementations for external systems
- Benefits for testing and technology changes

### 4. Onion Architecture
- Layers and dependency inversion
- Domain model at the center
- Application services and infrastructure
- Comparison with Clean and Hexagonal

---

## Assignment Deliverables

### 1. Presentation (20-30 minutes)
- Visual comparison of all architectural patterns
- Real-world use cases for each pattern
- Migration path from one pattern to another
- Decision matrix for selecting appropriate pattern

### 2. Code Demo/Example
- Simple application implemented in 2-3 different patterns
- Show folder structure for each approach
- Demonstrate how the same feature is implemented differently
- Highlight testability differences

### 3. Resources & References (Provide 3-5 curated)
- Robert C. Martin's Clean Architecture resources
- Hexagonal Architecture original article
- Framework-specific implementations (Spring, .NET Core, etc.)
- Video tutorials comparing these patterns

### 4. Q&A Session (10-15 minutes)
- Prepare for questions about pattern complexity
- Overhead vs benefits discussion
- How to introduce these patterns to existing projects
- Framework support and tooling

---

## Preparation Guidelines

### Research
- Study each pattern's original source material
- Find examples in your preferred programming language
- Understand the problem each pattern solves
- Review criticisms and limitations of each

### Practice
- Build a simple TODO app using Clean Architecture
- Refactor it to Hexagonal Architecture
- Compare with traditional MVC approach
- Create architecture diagrams for each

### Collaboration
- Discuss which patterns are used in current ATG projects
- Identify opportunities to introduce these patterns
- Share experiences with different approaches
- Debate pros/cons for your specific context

### Engagement
- Use interactive diagram to show dependency flow
- Live coding: refactor from one pattern to another
- Group discussion: which pattern for which scenario?
- Show how GitHub Copilot handles these patterns

---

## Success Criteria
- [ ] Clear explanation of at least 4 architectural patterns with diagrams
- [ ] Working code examples demonstrating pattern differences
- [ ] Practical decision framework for pattern selection
- [ ] Understanding of how patterns support testing and maintainability
- [ ] Team can identify which pattern(s) are used in current projects

---

## Support & Resources
- Reference the lesson_plan_template.md for structure
- Consult with architecture team about current patterns
- Review Clean Code and Clean Architecture books
- Software Engineering Excellence Framework document

---

## Notes
- This builds on Assignment 1.1 (Layered Architecture)
- Connect to SOLID principles (Category 5)
- These patterns are framework-agnostic but implementation varies
- Emphasize practical trade-offs over theoretical purity
- Discuss how AI coding assistants can help or hinder proper architecture
