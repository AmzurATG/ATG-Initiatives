# Assignment 9.2: Distributed Tracing

## Assignment Information
- **Category**: Logging & Monitoring
- **Sub-category**: 9.2 Distributed Tracing
- **Team Members**: [Pair names to be assigned]
- **Presentation Date**: [To be scheduled]
- **Duration**: 30-45 minutes

---

## Learning Objectives
By the end of this session, the team should be able to:
1. Understand distributed tracing in microservices
2. Implement correlation IDs for request tracking
3. Use distributed tracing tools effectively
4. Monitor performance across service boundaries

---

## Topics to Cover

### 1. Tracing in Microservices
- Why distributed tracing is needed
- Trace, span, and segment concepts
- Parent-child span relationships
- Trace context propagation
- Sampling strategies

### 2. Correlation IDs
- Generating unique request IDs
- Propagating IDs across services
- HTTP header standardization (X-Request-ID, X-Correlation-ID)
- Logging correlation IDs
- Tracing user journeys

### 3. Distributed Tracing Tools
- OpenTelemetry standard
- Jaeger architecture and setup
- Zipkin overview
- AWS X-Ray, Google Cloud Trace
- APM tools (DataDog, New Relic)
- Instrumentation approaches

### 4. Performance Monitoring
- Service dependency mapping
- Latency analysis
- Bottleneck identification
- Error rate tracking
- Service health visualization
- Alerting on performance degradation

---

## Assignment Deliverables

### 1. Presentation (20-30 minutes)
- Distributed tracing concepts with diagrams
- Correlation ID implementation
- Tracing tool comparison
- Performance analysis techniques
- Real-world tracing examples

### 2. Code Demo/Example
- Implement correlation ID middleware
- Set up OpenTelemetry
- Instrument services with tracing
- View traces in Jaeger/Zipkin
- Analyze service dependencies
- Show performance troubleshooting

### 3. Resources & References (Provide 3-5 curated)
- OpenTelemetry documentation
- Jaeger/Zipkin guides
- Distributed tracing patterns
- APM tool comparisons
- Microservices observability

### 4. Q&A Session (10-15 minutes)
- Prepare for questions about overhead
- Sampling vs full tracing
- Cost considerations
- Privacy and data retention
- Integration with existing systems

---

## Preparation Guidelines

### Research
- Study distributed tracing concepts
- Learn OpenTelemetry
- Understand tracing tools
- Review correlation patterns

### Practice
- Set up tracing infrastructure
- Instrument applications
- Generate and analyze traces
- Track requests across services
- Test performance monitoring

### Collaboration
- Review ATG microservices
- Identify tracing opportunities
- Discuss observability gaps
- Share tracing experiences

### Engagement
- Live tracing demonstration
- Interactive trace analysis
- Group: design tracing strategy
- Show real performance issues

---

## Success Criteria
- [ ] Understanding of distributed tracing
- [ ] Implementation of correlation IDs
- [ ] Setup of tracing tools
- [ ] Ability to analyze traces
- [ ] Performance monitoring skills

---

## Support & Resources
- Software Engineering Excellence Framework document
- OpenTelemetry documentation
- Tracing tool guides
- Observability resources

---

## Notes
- Critical for microservices
- Connect to microservices (Assignment 1.3)
- Relate to monitoring (Assignment 9.3)
- Discuss observability pillars
- Consider cloud-native tools
- Link to performance optimization
