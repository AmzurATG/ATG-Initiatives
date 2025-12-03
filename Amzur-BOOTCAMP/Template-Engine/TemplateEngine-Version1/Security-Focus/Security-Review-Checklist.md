# Security-Focused Code Review Template - Bootcamp Projects

## Project Information
- **Candidate Name:** [Name]
- **Project Title:** [Project Name]
- **Review Date:** [Date]
- **Reviewer:** [Reviewer Name]
- **Security Risk Level:** [Low/Medium/High/Critical]
- **Overall Security Score:** [X/10]

---

## Backend Security Review (Python FastAPI + PostgreSQL)

### 1. Authentication & Authorization
**Score: [X/10]**

#### Security Checklist:
- [ ] Strong password policy enforcement
- [ ] Multi-factor authentication implementation
- [ ] JWT token security (expiration, refresh, secret management)
- [ ] Session management security
- [ ] OAuth/OpenID Connect implementation
- [ ] Role-based access control (RBAC)
- [ ] Permission boundaries clearly defined
- [ ] Privilege escalation prevention

#### Critical Security Issues:
```
[List any authentication/authorization vulnerabilities found]
```

#### Recommendations:
- [Specific security improvements needed]

---

### 2. Input Validation & Data Security
**Score: [X/10]**

#### Security Checklist:
- [ ] Input sanitization implemented
- [ ] SQL injection prevention measures
- [ ] NoSQL injection prevention
- [ ] XSS protection mechanisms
- [ ] CSRF protection enabled
- [ ] File upload security (type validation, size limits)
- [ ] Parameter pollution prevention
- [ ] Command injection prevention

#### Critical Security Issues:
```
[List any input validation vulnerabilities]
```

#### Recommendations:
- [Specific validation improvements needed]

---

### 3. Database Security
**Score: [X/10]**

#### Security Checklist:
- [ ] Database connection security (SSL/TLS)
- [ ] Database credentials management
- [ ] SQL injection prevention in queries
- [ ] Database access control and permissions
- [ ] Sensitive data encryption at rest
- [ ] Database backup security
- [ ] Migration script security
- [ ] Database audit logging

#### Critical Security Issues:
```
[List any database security vulnerabilities]
```

#### Recommendations:
- [Specific database security improvements]

---

### 4. API Security
**Score: [X/10]**

#### Security Checklist:
- [ ] Rate limiting implementation
- [ ] API authentication mechanisms
- [ ] HTTPS enforcement
- [ ] CORS configuration security
- [ ] API versioning security considerations
- [ ] Error handling (no sensitive data exposure)
- [ ] Request/response validation
- [ ] API documentation security review

#### Critical Security Issues:
```
[List any API security vulnerabilities]
```

#### Recommendations:
- [Specific API security improvements]

---

### 5. Secrets & Configuration Security
**Score: [X/10]**

#### Security Checklist:
- [ ] Environment variables for secrets
- [ ] No hardcoded credentials in code
- [ ] Secure secret management system
- [ ] Configuration file security
- [ ] Docker secrets management
- [ ] API key rotation strategy
- [ ] Database connection string security
- [ ] Third-party service credentials security

#### Critical Security Issues:
```
[List any secrets management vulnerabilities]
```

#### Recommendations:
- [Specific secrets management improvements]

---

### 6. Error Handling & Logging Security
**Score: [X/10]**

#### Security Checklist:
- [ ] Error messages don't expose sensitive information
- [ ] Comprehensive security event logging
- [ ] Log data sanitization
- [ ] Audit trail for sensitive operations
- [ ] Log storage security
- [ ] Log access controls
- [ ] Security monitoring and alerting
- [ ] Incident response logging

#### Critical Security Issues:
```
[List any logging/error handling security issues]
```

#### Recommendations:
- [Specific logging security improvements]

---

## Frontend Security Review (React + Vite + Tailwind)

### 1. Client-Side Security
**Score: [X/10]**

#### Security Checklist:
- [ ] XSS prevention in React components
- [ ] Content Security Policy (CSP) implementation
- [ ] DOM-based XSS prevention
- [ ] Safe HTML rendering practices
- [ ] Input sanitization on client-side
- [ ] File upload security validation
- [ ] URL parameter validation
- [ ] Dynamic content security

#### Critical Security Issues:
```
[List any client-side security vulnerabilities]
```

#### Recommendations:
- [Specific client-side security improvements]

---

### 2. Authentication State Security
**Score: [X/10]**

#### Security Checklist:
- [ ] Secure token storage (not in localStorage)
- [ ] Token expiration handling
- [ ] Automatic logout on token expiry
- [ ] Secure authentication state management
- [ ] Protected route implementation
- [ ] Session timeout handling
- [ ] Multi-tab session synchronization
- [ ] Remember me functionality security

#### Critical Security Issues:
```
[List any authentication state vulnerabilities]
```

#### Recommendations:
- [Specific authentication state improvements]

---

### 3. Data Protection & Privacy
**Score: [X/10]**

#### Security Checklist:
- [ ] Sensitive data not exposed in client code
- [ ] PII data handling compliance
- [ ] Data masking for sensitive information
- [ ] Secure data transmission (HTTPS only)
- [ ] Client-side data encryption where needed
- [ ] Browser storage security (sessionStorage vs localStorage)
- [ ] Data retention policies implementation
- [ ] Cookie security settings

#### Critical Security Issues:
```
[List any data protection vulnerabilities]
```

#### Recommendations:
- [Specific data protection improvements]

---

### 4. Third-Party Dependencies Security
**Score: [X/10]**

#### Security Checklist:
- [ ] Dependency vulnerability scanning
- [ ] Minimal dependency principle
- [ ] Regular dependency updates
- [ ] Security patch management
- [ ] Third-party script validation
- [ ] CDN security considerations
- [ ] Package lock file security
- [ ] Supply chain attack prevention

#### Critical Security Issues:
```
[List any dependency security vulnerabilities]
```

#### Recommendations:
- [Specific dependency security improvements]

---

## Security Infrastructure & DevOps

### 1. Build & Deployment Security
**Score: [X/10]**

#### Security Checklist:
- [ ] Secure CI/CD pipeline configuration
- [ ] Build environment security
- [ ] Container security scanning
- [ ] Secrets in CI/CD security
- [ ] Production environment hardening
- [ ] Security headers configuration
- [ ] HTTPS certificate management
- [ ] Deployment rollback security

#### Critical Security Issues:
```
[List any build/deployment security issues]
```

#### Recommendations:
- [Specific build/deployment security improvements]

---

### 2. Monitoring & Incident Response
**Score: [X/10]**

#### Security Checklist:
- [ ] Security monitoring implementation
- [ ] Intrusion detection setup
- [ ] Anomaly detection mechanisms
- [ ] Security incident logging
- [ ] Automated threat response
- [ ] Security metrics tracking
- [ ] Vulnerability assessment process
- [ ] Incident response plan

#### Critical Security Issues:
```
[List any monitoring/incident response gaps]
```

#### Recommendations:
- [Specific monitoring/incident response improvements]

---

## Security Risk Assessment

### Critical Vulnerabilities (Immediate Action Required)
- [List critical security vulnerabilities that pose immediate risk]

### High-Risk Issues (Fix within 1 week)
- [List high-risk security issues]

### Medium-Risk Issues (Fix within 1 month)
- [List medium-risk security issues]

### Low-Risk Issues (Future improvements)
- [List low-risk security improvements]

---

## Security Compliance & Standards

### Industry Standards Adherence
- [ ] OWASP Top 10 compliance review
- [ ] Security by design principles
- [ ] Data protection regulations (GDPR, CCPA)
- [ ] Industry-specific security requirements
- [ ] Security documentation completeness

### Security Training Recommendations
- [Recommended security training topics for the candidate]

---

## Security Improvement Roadmap

### Phase 1 (Immediate - Week 1)
1. [Critical security fixes]
2. [Authentication improvements]
3. [Input validation enhancements]

### Phase 2 (Short-term - Month 1)
1. [Security monitoring implementation]
2. [Advanced security features]
3. [Security testing automation]

### Phase 3 (Long-term - Month 2-3)
1. [Security architecture improvements]
2. [Advanced threat protection]
3. [Security culture development]

---

**Security Assessment Summary:** [Overall security posture evaluation]

**Security Recommendation:** [Pass/Conditional Pass/Needs Security Review - with security-specific justification]

**Security Mentor Assignment:** [Recommend security-focused mentoring if needed]
