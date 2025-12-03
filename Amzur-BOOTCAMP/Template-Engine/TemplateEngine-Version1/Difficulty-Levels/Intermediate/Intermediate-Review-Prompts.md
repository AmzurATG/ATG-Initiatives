# Intermediate Level Code Review Prompts - AI Assistant Templates

## Overview
This document provides AI prompts specifically designed for reviewing intermediate-level bootcamp projects. Use these prompts to guide AI assistants in providing thorough, constructive feedback for candidates demonstrating intermediate programming skills.

---

## Backend Review Prompts (Python FastAPI + PostgreSQL)

### 1. Advanced Database Architecture Review
```
Please review the database models and architecture in this intermediate-level project:

**Focus Areas:**
- Analyze the complexity of entity relationships and foreign key constraints
- Evaluate indexing strategies for query performance
- Check for proper handling of database migrations with data preservation
- Assess connection pooling and database optimization techniques
- Review query optimization and prevention of N+1 problems

**Evaluation Criteria:**
- Can handle complex many-to-many relationships and self-referencing models
- Demonstrates understanding of database performance optimization
- Shows awareness of data integrity and consistency
- Implements proper migration strategies for production systems

**Provide specific feedback on:**
1. Database schema design maturity
2. Performance optimization techniques used
3. Areas where advanced database concepts could be better implemented
4. Suggestions for scaling the database architecture

Please score each aspect from 1-10 and provide actionable recommendations.
```

### 2. API Design Patterns & Architecture
```
Evaluate the API design and implementation for this intermediate FastAPI project:

**Assessment Points:**
- Advanced FastAPI features utilization (dependencies, middleware, background tasks)
- Custom exception handling and error response patterns
- Implementation of authentication and authorization systems
- Rate limiting and API security measures
- WebSocket or real-time feature implementation
- API versioning and documentation quality

**Look for evidence of:**
- Understanding of REST principles and HTTP status codes
- Proper use of FastAPI's dependency injection system
- Implementation of caching strategies
- Background task processing
- Custom middleware development

**Provide detailed analysis of:**
1. API architecture maturity and scalability
2. Security implementation completeness
3. Performance considerations and optimizations
4. Code organization and maintainability
5. Areas for improvement toward advanced-level API design

Rate each component and suggest specific enhancements.
```

### 3. Business Logic & Service Architecture
```
Review the business logic and service layer architecture:

**Focus on:**
- Implementation of design patterns (Factory, Strategy, Observer, etc.)
- Service layer abstraction and separation of concerns
- Domain-driven design principles application
- Error handling and custom exception strategies
- Transaction management across services
- Event-driven architecture implementation

**Evaluate:**
- Code organization and maintainability
- Adherence to SOLID principles
- Testability of business logic
- Configuration and dependency management
- Logging and monitoring integration

**Provide feedback on:**
1. Architectural decision quality
2. Code complexity management
3. Scalability considerations
4. Testing strategy effectiveness
5. Recommendations for advanced architectural patterns

Score each area and provide actionable improvement suggestions.
```

---

## Frontend Review Prompts (React + Vite + Tailwind)

### 1. Advanced React Patterns & Architecture
```
Analyze the React component architecture and advanced patterns usage:

**Review Areas:**
- Implementation of advanced React patterns (HOCs, Render Props, Compound Components)
- Custom hooks design and reusability
- Context API usage for state management
- Error boundaries and error handling strategies
- Performance optimization techniques (memo, useMemo, useCallback)
- Portal usage and advanced component composition

**Assessment Criteria:**
- Component reusability and maintainability
- Proper abstraction levels
- Performance considerations
- Code splitting and lazy loading implementation

**Provide detailed evaluation of:**
1. Component architecture maturity
2. React patterns implementation quality
3. Performance optimization effectiveness
4. Code organization and scalability
5. Areas for advancement to senior-level React development

Rate each aspect and suggest specific improvements.
```

### 2. State Management & Data Flow
```
Evaluate the state management implementation and data flow patterns:

**Focus Areas:**
- Advanced state management solution (Redux Toolkit, Zustand, Context)
- State normalization and organization
- Async state handling and loading patterns
- Real-time data synchronization
- Form state management complexity
- State persistence and hydration strategies

**Look for:**
- Proper separation of local vs global state
- Optimistic updates implementation
- Error state handling
- Performance considerations in state updates
- Testing strategies for state management

**Analyze:**
1. State architecture scalability
2. Data flow clarity and maintainability
3. Performance impact of state management choices
4. Error handling robustness
5. Recommendations for complex application state management

Provide scores and actionable feedback for each area.
```

### 3. Performance & Optimization Strategies
```
Review the performance optimization techniques and implementation:

**Assessment Points:**
- Bundle size optimization and code splitting strategies
- Image optimization and lazy loading implementation
- Virtual scrolling for large datasets
- Service worker and PWA features
- Memory leak prevention
- Rendering optimization techniques

**Evaluate:**
- Build configuration optimization (Vite)
- Runtime performance monitoring
- Accessibility implementation
- SEO considerations
- Progressive enhancement strategies

**Provide analysis of:**
1. Performance optimization maturity
2. Build and deployment efficiency
3. User experience considerations
4. Scalability for larger applications
5. Advanced optimization opportunities

Score each component and recommend specific performance improvements.
```

---

## Testing Strategy Prompts

### Comprehensive Testing Review
```
Evaluate the testing strategy and implementation for this intermediate project:

**Testing Areas to Review:**
- Unit test coverage and quality (backend: pytest, frontend: Jest/Vitest)
- Integration testing implementation
- API endpoint testing comprehensiveness
- Component testing with complex scenarios
- E2E testing strategy
- Mock strategies for external dependencies

**Assessment Criteria:**
- Test organization and maintainability
- Coverage of edge cases and error scenarios
- Performance testing implementation
- Security testing considerations
- Test automation and CI/CD integration

**Analyze:**
1. Testing strategy completeness
2. Quality of test implementations
3. Coverage gaps and improvement opportunities
4. Testing tool utilization effectiveness
5. Recommendations for advanced testing practices

Provide detailed feedback and scoring for testing maturity.
```

---

## DevOps & Deployment Prompts

### Infrastructure & Deployment Review
```
Review the DevOps practices and deployment readiness:

**Focus Areas:**
- Containerization implementation (Docker)
- Environment configuration management
- CI/CD pipeline setup
- Database migration strategies
- Logging and monitoring implementation
- Security considerations in deployment

**Evaluate:**
- Health checks and readiness probes
- Backup and recovery strategies
- Scalability considerations
- Security scanning integration
- Documentation quality for deployment

**Provide assessment of:**
1. Deployment strategy maturity
2. Infrastructure as code implementation
3. Monitoring and observability setup
4. Security best practices adherence
5. Readiness for production deployment

Score each area and suggest improvements for production-ready systems.
```

---

## Overall Project Assessment Prompt

### Comprehensive Intermediate-Level Evaluation
```
Conduct a comprehensive review of this intermediate-level bootcamp project:

**Overall Assessment Framework:**
1. **Technical Complexity:** Evaluate the complexity of implemented features and architectural decisions
2. **Code Quality:** Assess code organization, maintainability, and adherence to best practices
3. **Performance:** Review optimization techniques and scalability considerations
4. **Security:** Analyze security implementation and vulnerability prevention
5. **Testing:** Evaluate testing strategy comprehensiveness and quality
6. **Documentation:** Review code documentation and project setup instructions

**Provide:**
- Detailed strengths analysis highlighting intermediate-level competencies
- Specific areas for improvement with actionable recommendations
- Learning path suggestions for advancement to senior-level skills
- Overall readiness assessment for complex, production-level projects
- Score breakdown for each major area (1-10 scale)

**Final Assessment Should Include:**
- Technical skill level confirmation (beginner/intermediate/advanced)
- Career readiness evaluation
- Specific recommendations for continued learning
- Priority areas for immediate improvement

Please provide a comprehensive evaluation that will help guide the candidate's continued learning journey.
```

---

## Usage Guidelines

### For Reviewers:
1. Select prompts based on specific review focus areas
2. Customize prompts based on project complexity
3. Combine multiple prompts for comprehensive reviews
4. Use scoring criteria consistently across reviews

### For AI Assistants:
1. Provide specific, actionable feedback
2. Include code examples where helpful
3. Reference best practices and industry standards
4. Suggest learning resources for improvement areas

### For Continuous Improvement:
1. Track common feedback patterns
2. Update prompts based on review outcomes
3. Adjust difficulty expectations based on cohort performance
4. Incorporate new technology trends and best practices
