# Security Review - Condensed Prompt

## Single Comprehensive Security Review
**Save output as:** `security-review/complete-security-review.md`

```
You are a cybersecurity expert reviewing a bootcamp FastAPI + React application. Analyze #codebase for security vulnerabilities and risks.

**Analysis Areas (Score each 1-10):**

1. **Threat Surface**: Entry points (API endpoints, forms, uploads), data flow (PII, credentials), trust boundaries, attack vectors, critical assets
2. **Authentication & Authorization**: Identity verification, session management (token handling, expiration, storage), RBAC, privilege escalation risks, account security
3. **Injection Prevention**: Input validation (client & server), SQL/NoSQL/Command/XSS injection risks, data sanitization, output encoding
4. **Data Protection**: Sensitive data handling (PII, credentials, tokens), encryption (at rest/in transit), data exposure risks, privacy compliance (GDPR/CCPA)
5. **Infrastructure Security**: Secrets management, environment config, dependency vulnerabilities, build/deployment security, network security (CORS, CSP, headers)
6. **Error Handling**: Error response content, logging security, debug info exposure, stack traces, information enumeration
7. **Frontend Security**: XSS prevention, client storage (token storage), CSP, third-party integrations, DOM security
8. **API Security**: Rate limiting, API authentication, CORS configuration, response data leakage
9. **Business Logic**: Workflow security, race conditions, state management, financial operations, file operations
10. **Monitoring**: Security event logging, anomaly detection, audit trails, incident response readiness

**Output Format:**
- Score (X/10) + key findings for each area
- Top 5 vulnerabilities with severity (Critical/High/Medium/Low), file:line, exploit scenario
- Top 5 security improvements with implementation steps
- Compliance gaps & remediation
- Security maturity: Critical(1-2)/Poor(3-4)/Adequate(5-6)/Good(7-8)/Strong(9-10)

Focus on: exploitable vulnerabilities, specific code locations, concrete attack scenarios, OWASP Top 10.
```

## Quick Vulnerability Scan
**Use for focused security check:**

```
Analyze #codebase for common security vulnerabilities:
1. Authentication/authorization issues (weak auth, broken access control)
2. Injection vulnerabilities (SQL, XSS, command injection)
3. Sensitive data exposure (hardcoded secrets, exposed PII, insecure storage)
4. Security misconfigurations (CORS, CSP, error messages)
5. Input validation gaps

Provide: Top 5 vulnerabilities with severity (Critical/High/Medium/Low), file:line, fix recommendations.
```

## Token Usage Tips
- Focus on high-risk areas: Authentication, input validation, data protection
- Use file patterns: `#file:**/auth/**`, `#file:**/*login*`, `#file:**/api/**`
- Request specific vulnerability types: "Check for SQL injection and XSS only"
