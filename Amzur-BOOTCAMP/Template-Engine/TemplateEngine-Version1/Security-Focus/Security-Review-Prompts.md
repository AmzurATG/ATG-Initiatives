# Security-Focused Code Review Prompt Template

## Security-Focused Code Review System for Bootcamp Projects

This template provides AI prompts specifically designed for comprehensive security review of FastAPI + React applications in bootcamp settings.

---

## Security Review Prompt Collection

### 0. Security Context Analysis
**Save output as:** `security-review/00-security-context-analysis.md`

```
You are a cybersecurity expert and application security architect conducting a comprehensive security assessment for a bootcamp candidate's full-stack application. Your goal is to identify security vulnerabilities, assess threat landscape, and provide security-focused recommendations.

**Security Assessment Scope:** Use #codebase to comprehensively scan for security-related patterns, vulnerabilities, and risks across the entire application.

**Security Analysis Framework:**

### 1. Threat Modeling & Attack Surface Analysis
- **Application Entry Points**: Identify all API endpoints, forms, file uploads, authentication flows
- **Data Flow Mapping**: Trace sensitive data through the application (PII, credentials, tokens)
- **Trust Boundaries**: Identify where data crosses security boundaries (client/server, database, external services)
- **Attack Vectors**: Potential ways attackers could compromise the application
- **Asset Inventory**: Critical data, systems, and components that need protection

### 2. Authentication & Authorization Security Assessment
- **Authentication Mechanisms**: How users prove their identity
- **Session Management**: Token handling, expiration, storage, renewal
- **Authorization Patterns**: Role-based access control, permission systems
- **Privilege Escalation Risks**: Potential for users to gain unauthorized access
- **Account Security**: Password policies, account lockout, brute force protection

### 3. Input Validation & Injection Attack Prevention
- **Input Vectors**: All places where user data enters the system
- **Validation Patterns**: Server-side and client-side validation implementation
- **Injection Vulnerabilities**: SQL, NoSQL, Command, LDAP, XSS injection risks
- **Data Sanitization**: How user input is cleaned and processed
- **Output Encoding**: How data is safely rendered to prevent XSS

### 4. Data Protection & Privacy Analysis
- **Sensitive Data Identification**: PII, credentials, tokens, business data
- **Data at Rest Security**: Database encryption, file storage security
- **Data in Transit Security**: HTTPS, API communication, WebSocket security
- **Data Exposure Risks**: Information leakage through logs, errors, APIs
- **Privacy Compliance**: GDPR, CCPA considerations

### 5. Infrastructure & Configuration Security
- **Secrets Management**: How API keys, passwords, certificates are handled
- **Environment Configuration**: Security settings in development vs production
- **Dependency Security**: Third-party package vulnerabilities
- **Build & Deployment Security**: CI/CD pipeline security, container security
- **Network Security**: CORS, CSP, security headers

### 6. Error Handling & Information Disclosure
- **Error Response Analysis**: What information errors reveal to attackers
- **Logging Security**: What gets logged and who has access
- **Debug Information**: Development artifacts in production
- **Stack Trace Exposure**: Technical details leaked to users
- **Information Enumeration**: User/system information disclosure

### 7. Frontend Security Assessment
- **XSS Prevention**: How React components handle user content
- **Client-Side Storage**: Token storage, sensitive data in browser
- **Content Security Policy**: CSP implementation and effectiveness
- **Third-Party Integration**: External scripts, CDNs, analytics security
- **DOM Security**: DOM manipulation and client-side vulnerabilities

### 8. API Security Evaluation
- **Rate Limiting**: Protection against abuse and DoS attacks
- **API Authentication**: How APIs verify client identity
- **CORS Configuration**: Cross-origin request security
- **API Versioning**: Security implications of version management
- **Response Security**: Data leakage through API responses

### 9. Business Logic Security
- **Workflow Security**: How business processes handle security
- **Race Conditions**: Concurrent access security issues
- **State Management**: Secure handling of application state
- **Financial Transactions**: Payment and monetary operation security
- **File Operations**: Upload, download, processing security

### 10. Security Monitoring & Incident Response
- **Security Event Logging**: What security events are captured
- **Anomaly Detection**: Unusual behavior identification
- **Audit Trails**: Accountability and forensic capabilities
- **Incident Response**: How security incidents would be handled
- **Security Metrics**: Measurement of security posture

**Security Risk Prioritization:**
- Critical (Immediate security risk): Score 9-10
- High (Significant security concern): Score 7-8
- Medium (Security improvement needed): Score 5-6
- Low (Security enhancement): Score 3-4
- Minimal (No immediate security impact): Score 1-2

**Output Requirements:**
- Comprehensive security threat assessment
- Vulnerability inventory with severity ratings
- Attack scenario mapping
- Security architecture evaluation
- Compliance gap analysis
- Security improvement roadmap

Please conduct a thorough security analysis of #codebase and establish the security baseline for subsequent detailed security reviews.
```

### 1. Authentication & Authorization Security Review
**Save output as:** `security-review/01-authentication-security.md`

```
You are an identity and access management (IAM) security expert reviewing authentication and authorization implementation in this FastAPI application.

**Context Preparation:** First read `security-review/00-security-context-analysis.md` to understand the security landscape and threat model.

**Authentication Files to Review:** Use #codebase to identify authentication-related files:
- Authentication middleware and dependencies
- JWT/token handling implementation
- User registration and login endpoints
- Password management utilities
- Role and permission management

**Security Assessment Framework:**

### Authentication Security Analysis
- Password security implementation (hashing, complexity, storage)
- Multi-factor authentication availability
- Account lockout and brute force protection
- Token generation and validation security
- Session management and timeout handling
- Authentication bypass vulnerabilities

### Authorization Security Analysis
- Role-based access control implementation
- Permission verification at endpoint level
- Privilege escalation prevention
- Authorization token validation
- Resource-level access control
- Administrative privilege separation

### Common Vulnerability Assessment
- Weak password policies
- Insecure token storage or transmission
- Missing authorization checks
- Privilege escalation paths
- Session fixation vulnerabilities
- Authentication bypass opportunities

**Security Risk Rating:** Rate each finding with:
- CRITICAL (9-10): Immediate authentication bypass or privilege escalation
- HIGH (7-8): Significant authentication weakness
- MEDIUM (5-6): Authentication improvement needed
- LOW (3-4): Authentication enhancement opportunity

Provide specific code examples of vulnerabilities and secure implementation recommendations.
```

### 2. Input Validation & Injection Security Review
**Save output as:** `security-review/02-input-validation-security.md`

```
You are an application security specialist focusing on input validation and injection attack prevention for this FastAPI + React application.

**Context Preparation:** Read the security context analysis to understand attack vectors and input handling patterns.

**Input Handling Files to Review:** Use #codebase to locate:
- Pydantic schema validation models
- FastAPI route parameter handling
- Database query implementations
- File upload handling
- Frontend form validation

**Injection Vulnerability Assessment:**

### SQL Injection Analysis
- Parameterized query usage in database operations
- Dynamic SQL construction patterns
- ORM query security (SQLAlchemy)
- Raw SQL query security review
- Database connection security

### Cross-Site Scripting (XSS) Prevention
- React component XSS prevention patterns
- User content rendering security
- HTML sanitization implementation
- Dynamic content generation security
- Template injection vulnerabilities

### Command Injection Assessment
- System command execution patterns
- File system operation security
- External process invocation
- Shell command construction
- Environment variable injection

### NoSQL Injection Analysis
- Document database query security
- JSON parameter injection
- MongoDB/document store query patterns
- Dynamic query construction

**Validation Security Review:**
- Server-side validation completeness
- Client-side validation bypass prevention
- Input sanitization effectiveness
- Output encoding implementation
- Data type validation security

**Security Scoring:** Each vulnerability type scored 1-10 with remediation priority.
```

### 3. Data Protection & Privacy Security Review
**Save output as:** `security-review/03-data-protection-security.md`

```
You are a data protection and privacy security expert reviewing data handling practices in this full-stack application.

**Data Security Analysis Scope:** Use #codebase to examine:
- Database models and sensitive data fields
- API response data exposure
- File storage and handling
- Client-side data storage
- Logging and error handling data exposure

**Data Protection Assessment:**

### Sensitive Data Identification
- Personal Identifiable Information (PII) handling
- Financial data protection
- Authentication credentials storage
- Business sensitive information
- User behavioral data

### Data at Rest Security
- Database encryption implementation
- File storage security
- Backup data protection
- Local storage security
- Configuration file security

### Data in Transit Security
- HTTPS enforcement
- API communication encryption
- WebSocket security
- File transfer security
- Internal service communication

### Data Exposure Prevention
- API response data filtering
- Error message data leakage
- Log file data exposure
- Debug information leakage
- Client-side data exposure

### Privacy Compliance Analysis
- GDPR compliance considerations
- Data retention policies
- User consent management
- Data deletion capabilities
- Privacy by design implementation

**Risk Assessment:**
- CRITICAL: PII/financial data exposure
- HIGH: Authentication data compromise
- MEDIUM: Business data leakage
- LOW: Non-sensitive data handling improvements

Provide specific remediation strategies for each data protection gap identified.
```

### 4. API Security Review
**Save output as:** `security-review/04-api-security.md`

```
You are an API security specialist conducting a comprehensive security assessment of FastAPI endpoints and API design.

**API Security Analysis Scope:** Use #codebase to review:
- FastAPI route definitions and handlers
- API authentication and authorization
- Request/response handling
- Rate limiting implementation
- CORS configuration

**API Security Assessment Framework:**

### Endpoint Security Analysis
- Authentication requirement enforcement
- Authorization verification per endpoint
- HTTP method security (GET, POST, PUT, DELETE)
- Parameter validation and sanitization
- Response data filtering

### Rate Limiting & DoS Protection
- Request rate limiting implementation
- API abuse prevention mechanisms
- Resource consumption controls
- Concurrent request handling
- DDoS mitigation strategies

### CORS & Cross-Origin Security
- CORS policy configuration
- Allowed origins security
- Preflight request handling
- Credential handling in CORS
- Cross-origin data exposure

### API Authentication Security
- API key management and rotation
- Bearer token validation
- OAuth implementation security
- API versioning security implications
- Webhook security (if applicable)

### Error Handling & Information Disclosure
- Error response standardization
- Stack trace exposure prevention
- API documentation security
- Debug endpoint exposure
- Internal system information leakage

**Security Testing Recommendations:**
- Automated security scanning
- Penetration testing focus areas
- Security regression testing
- API fuzzing strategies

Score each API security aspect 1-10 and provide hardening recommendations.
```

### 5. Frontend Security Review
**Save output as:** `security-review/05-frontend-security.md`

```
You are a frontend security expert specializing in React application security and client-side vulnerability assessment.

**Frontend Security Scope:** Use #codebase to analyze:
- React component security patterns
- Client-side authentication handling
- Browser storage usage
- Third-party dependency security
- Build configuration security

**Frontend Security Assessment:**

### XSS Prevention Analysis
- React XSS protection mechanisms
- dangerouslySetInnerHTML usage review
- Dynamic content rendering security
- User-generated content handling
- HTML sanitization implementation

### Client-Side Authentication Security
- Token storage mechanisms (localStorage vs sessionStorage vs cookies)
- Authentication state management
- Token refresh handling
- Logout functionality security
- Session timeout implementation

### Content Security Policy (CSP)
- CSP header implementation
- Script source restrictions
- Style source security
- Image and media source controls
- Inline script/style prevention

### Third-Party Security
- Dependency vulnerability assessment
- CDN security considerations
- External script loading security
- Analytics and tracking security
- Social media integration security

### Browser Security Features
- Security header implementation
- Cookie security attributes
- Referrer policy configuration
- Feature policy implementation
- Subresource Integrity (SRI)

### Build & Deployment Security
- Environment variable exposure
- Source map security
- Bundle security analysis
- Development artifact removal
- Production hardening

**Client-Side Vulnerability Scoring:**
- CRITICAL: XSS vulnerabilities, token exposure
- HIGH: Authentication bypass, sensitive data exposure
- MEDIUM: Security header missing, dependency vulnerabilities
- LOW: Security enhancements, monitoring improvements

Provide React-specific security implementation examples and remediation code.
```

### 6. Infrastructure & Configuration Security Review
**Save output as:** `security-review/06-infrastructure-security.md`

```
You are a DevSecOps expert reviewing infrastructure security, configuration management, and deployment security practices.

**Infrastructure Security Scope:** Use #codebase to examine:
- Environment configuration files
- Docker and containerization security
- CI/CD pipeline configuration
- Secrets management implementation
- Production deployment security

**Infrastructure Security Assessment:**

### Secrets Management Review
- Environment variable usage
- API key and credential storage
- Database connection security
- Third-party service authentication
- Secret rotation capabilities

### Container Security Analysis
- Dockerfile security best practices
- Base image security
- Container runtime security
- Volume mount security
- Network security configuration

### CI/CD Pipeline Security
- Build environment security
- Deployment process security
- Secret handling in CI/CD
- Code scanning integration
- Artifact security

### Production Environment Security
- Server hardening assessment
- Network security configuration
- Monitoring and logging security
- Backup and recovery security
- Incident response capabilities

### Configuration Security
- Security header configuration
- Database security settings
- Web server security configuration
- Firewall and network rules
- SSL/TLS configuration

**Infrastructure Risk Assessment:**
- CRITICAL: Exposed secrets, insecure defaults
- HIGH: Container vulnerabilities, weak network security
- MEDIUM: Missing monitoring, configuration gaps
- LOW: Security documentation, process improvements

Provide infrastructure hardening recommendations and security automation suggestions.
```

### 7. Comprehensive Security Report Aggregation
**Save output as:** `security-review/99-COMPREHENSIVE-SECURITY-REPORT.md`

```
You are the Chief Information Security Officer (CISO) compiling a comprehensive security assessment report for this bootcamp candidate's application.

**Report Compilation:** Read all security review files from `security-review/` folder:
- 00-security-context-analysis.md
- 01-authentication-security.md
- 02-input-validation-security.md
- 03-data-protection-security.md
- 04-api-security.md
- 05-frontend-security.md
- 06-infrastructure-security.md

**Executive Security Summary:**

### Security Posture Assessment
- Overall security maturity score (1-10)
- Critical vulnerability count and risk level
- Security compliance grade
- Threat landscape assessment
- Business risk evaluation

### Vulnerability Summary by Severity
- **CRITICAL (9-10)**: Immediate security threats requiring emergency response
- **HIGH (7-8)**: Significant vulnerabilities requiring urgent attention
- **MEDIUM (5-6)**: Important security improvements needed
- **LOW (3-4)**: Security enhancements and best practices

### Security Risk Matrix
Create a risk matrix showing:
- Probability of exploitation
- Impact severity
- Current mitigation status
- Recommended timeline for remediation

### Industry Benchmark Comparison
- Security maturity vs. industry standards
- Best practice adoption rate
- Compliance gap analysis
- Peer comparison insights

### Security Investment Priorities
- **Phase 1 (Immediate - 1 week)**: Critical security fixes
- **Phase 2 (Short-term - 1 month)**: High-priority improvements
- **Phase 3 (Medium-term - 3 months)**: Security program maturation
- **Phase 4 (Long-term - 6 months)**: Advanced security capabilities

### Bootcamp Security Learning Path
- Critical security skills gaps
- Recommended security training
- Hands-on security exercises
- Security-focused project additions
- Security mentoring recommendations

### Compliance & Governance
- Data protection regulation compliance
- Industry security standard adherence
- Security policy development needs
- Audit trail and documentation gaps

### Security Culture Assessment
- Security awareness level
- Secure development practices adoption
- Security testing integration
- Incident response preparedness

**Final Security Recommendation:**
- **PRODUCTION READY**: Minimal security risks, deploy with confidence
- **SECURITY REVIEW REQUIRED**: Address critical/high issues before deployment
- **MAJOR SECURITY OVERHAUL NEEDED**: Significant security redesign required
- **SECURITY MENTORING REQUIRED**: Intensive security training and guidance needed

Provide actionable security roadmap with specific timelines, resources, and success metrics.
```

---

## Security Review Implementation Workflow

### Phase 1: Security Assessment Setup (5 minutes)
1. Create `security-review/` folder in project root
2. Ensure GitHub Copilot access to full codebase
3. Run Security Context Analysis prompt (Prompt #0)

### Phase 2: Detailed Security Reviews (45-60 minutes)
1. **Authentication Security** (10 minutes) - Prompt #1
2. **Input Validation Security** (10 minutes) - Prompt #2  
3. **Data Protection Security** (10 minutes) - Prompt #3
4. **API Security** (10 minutes) - Prompt #4
5. **Frontend Security** (10 minutes) - Prompt #5
6. **Infrastructure Security** (10 minutes) - Prompt #6

### Phase 3: Security Report Compilation (10-15 minutes)
1. Run Comprehensive Security Report prompt (Prompt #7)
2. Review and customize for bootcamp context
3. Generate security improvement roadmap

### Phase 4: Security Mentoring Plan (5 minutes)
1. Identify critical security learning gaps
2. Create personalized security training plan
3. Schedule security-focused mentoring sessions
4. Provide security resources and tools

## Security Review Quality Assurance

### Before Starting Security Review:
- [ ] All security-focused prompts ready
- [ ] #codebase access confirmed
- [ ] Security review folder structure created

### During Security Reviews:
- [ ] Each prompt addresses specific security domain
- [ ] Vulnerability severity consistently rated
- [ ] Remediation recommendations provided
- [ ] Code examples included where helpful

### After All Security Reviews:
- [ ] All security domains covered
- [ ] Consistent risk rating across reviews
- [ ] Actionable recommendations provided
- [ ] Integration with overall bootcamp assessment

### Security Report Quality Check:
- [ ] Executive summary with clear risk assessment
- [ ] Prioritized vulnerability list
- [ ] Specific remediation timeline
- [ ] Security learning path provided
- [ ] Compliance and governance considerations
- [ ] Security culture development recommendations

This security-focused template ensures comprehensive security assessment while maintaining educational value for bootcamp candidates.
