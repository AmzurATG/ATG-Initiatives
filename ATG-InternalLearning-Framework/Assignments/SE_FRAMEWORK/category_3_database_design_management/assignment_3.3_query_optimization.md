# Assignment 3.3: Query Optimization

## Assignment Information
- **Category**: Database Design & Management
- **Sub-category**: 3.3 Query Optimization
- **Team Members**: [Pair names to be assigned]
- **Presentation Date**: [To be scheduled]
- **Duration**: 30-45 minutes

---

## Learning Objectives
By the end of this session, the team should be able to:
1. Design and use database indexes effectively
2. Analyze and optimize query execution plans
3. Identify and fix the N+1 query problem
4. Apply database performance tuning techniques

---

## Topics to Cover

### 1. Index Strategies and Types
- B-tree indexes (most common)
- Hash indexes
- Full-text indexes
- Composite indexes and column order
- Covering indexes
- When indexes hurt performance

### 2. Query Execution Plans
- Reading EXPLAIN/EXPLAIN ANALYZE output
- Sequential scans vs index scans
- Join strategies (nested loop, hash, merge)
- Cost estimation and statistics
- Query planner hints (when necessary)

### 3. N+1 Query Problem
- What is the N+1 problem
- Impact on application performance
- Detection techniques
- Solutions: eager loading, batch loading, DataLoader
- ORM-specific solutions

### 4. Database Performance Tuning
- Query rewriting for optimization
- Subquery vs JOIN performance
- Avoiding SELECT *
- Connection pooling
- Caching strategies
- Database-specific optimizations

---

## Assignment Deliverables

### 1. Presentation (20-30 minutes)
- Index fundamentals with visual examples
- How to read execution plans
- N+1 problem demonstration and solutions
- Performance tuning checklist
- Real-world optimization case studies

### 2. Code Demo/Example
- Create indexes and show performance impact
- Analyze query plans before and after optimization
- Demonstrate N+1 problem and fix
- Use database profiling tools
- Optimize slow queries from sample application

### 3. Resources & References (Provide 3-5 curated)
- Use The Index, Luke (online book)
- Database-specific optimization guides
- ORM query optimization documentation
- Performance monitoring tools
- Query optimization case studies

### 4. Q&A Session (10-15 minutes)
- Prepare for questions about index maintenance cost
- Query hints and forced indexes
- Partitioning and sharding
- Read replicas and query routing
- Cloud database optimization

---

## Preparation Guidelines

### Research
- Study how indexes work internally
- Learn to read execution plans for your database
- Understand join algorithms
- Review ORM query generation

### Practice
- Create slow queries and optimize them
- Use EXPLAIN to analyze queries
- Add appropriate indexes
- Fix N+1 problems in sample code
- Benchmark performance improvements

### Collaboration
- Profile ATG application queries
- Identify slow queries in production
- Review indexing strategies
- Share optimization wins and challenges

### Engagement
- Live query optimization session
- Before/after performance comparison
- Interactive execution plan analysis
- Group exercise: optimize a slow query

---

## Success Criteria
- [ ] Understanding of index types and when to use them
- [ ] Ability to read and interpret execution plans
- [ ] Recognition and fixing of N+1 query problems
- [ ] Practical query optimization techniques
- [ ] Team can profile and optimize database queries

---

## Support & Resources
- Reference the lesson_plan_template.md for structure
- Consult with database performance experts
- Use database profiling tools
- Software Engineering Excellence Framework document

---

## Notes
- Critical for application performance at scale
- Connect to performance optimization category (12)
- Relate to monitoring and observability (Category 9)
- Emphasize measure-first approach
- Discuss how ORMs can hide performance issues
- Consider database-specific features (PostgreSQL vs MySQL)
