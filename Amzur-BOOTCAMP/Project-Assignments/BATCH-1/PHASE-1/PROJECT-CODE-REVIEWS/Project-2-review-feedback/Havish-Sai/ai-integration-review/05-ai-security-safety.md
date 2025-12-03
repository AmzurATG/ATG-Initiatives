# AI Security & Safety Implementation Review: Web Content Analyzer

## Project Information
- **Project Title:** Web Content Analyzer  
- **Name:** Havish Sai
- **Review Date:** August 7th, 2025
- **Reviewer:** Github Copilot


## Executive Summary

This review examines the AI security and safety implementation in the Web Content Analyzer application. The application integrates OpenAI's API to analyze scraped web content but exhibits several critical gaps in AI security practices. While the application has a functional foundation, it lacks essential protections against prompt injection, content filtering, data privacy safeguards, and other AI-specific security measures.

**Overall AI Security & Safety Score: 3/10**

The application has implemented basic OpenAI API integration but lacks comprehensive AI security measures. There is minimal input sanitization, no prompt injection prevention, absent content safety filtering, and limited data privacy protections. Implementing structured input validation, prompt injection detection, content moderation, and robust error handling would significantly improve the security posture of the AI integration.

---

## Input Security & Prompt Injection Prevention

### Input Sanitization and Validation for AI Prompts
**Score: 2/10**

The application performs minimal sanitization of web content before sending it to the OpenAI API:

```python
# From src/scrapers/content_extractor.py and src/processors/content_cleaner.py
def normalize_text(s: str) -> str:
    s = re.sub(r"\r\n?", "\n", s)
    s = re.sub(r"\n{3,}", "\n\n", s)
    s = re.sub(r"\s{2,}", " ", s)
    return s.strip()
```

**Strengths:**
- Basic text normalization to handle whitespace and line breaks
- HTML tag removal during content extraction

**Weaknesses:**
- No specific sanitization for AI-specific security risks
- No detection of prompt injection attempts
- No validation of content against known attack patterns
- No removal of adversarial inputs that could manipulate the AI
- No limits on specific content types that could trigger harmful responses

### Prompt Injection Attack Detection and Prevention
**Score: 1/10**

There is no evident implementation to detect or prevent prompt injection attacks:

**Weaknesses:**
- No boundary enforcement between system and user content
- No detection of common prompt injection patterns
- No filtering of control sequences or special instructions
- No separation of trusted system prompts from user input
- No validation against known prompt injection techniques
- No implementation of prompt sandboxing or containerization

### User Input Filtering and Content Validation
**Score: 3/10**

Some basic content validation is implemented but not specifically for AI security:

```python
# From src/scrapers/web_scraper.py - content type validation
ctype = (r.headers.get("content-type") or "text/html").split(";")[0].strip().lower()
accept = any(ctype.startswith(p) for p in ALLOWED_CT_PREFIXES) or ctype.endswith("+xml")
if not accept:
    raise ScrapeError(f"Unsupported content-type: {ctype}")
```

**Strengths:**
- Content type validation for scraped content
- Size limitations on content (MAX_BYTES = 2_500_000)

**Weaknesses:**
- No AI-specific content filtering
- No detection of harmful, illegal, or manipulative content
- No semantic analysis of content before AI processing
- No filtering of content that could elicit harmful AI outputs
- No validation of structured content against schema

### Context Isolation and Boundary Enforcement
**Score: 2/10**

The application lacks explicit context isolation between system prompts and user content:

```python
# Inferred from usage of LLMClient in analysis_service.py
system_prompt = "You are an expert content analyzer..."
user_prompt = f"""Analyze the following web content from {content.url}:
Title: {content.title}
Content: {content.main_text[:max_chars]}
"""
```

**Strengths:**
- Separate system and user prompts in the API call structure

**Weaknesses:**
- No additional boundary enforcement between system and user content
- No isolation of user content within the prompt
- No verification that user content doesn't override system instructions
- No parameter validation or escaping to prevent context leakage
- No mechanisms to prevent prompt takeover attacks

### Escape Sequence Detection and Neutralization
**Score: 1/10**

There is no implementation to detect or neutralize escape sequences that could break out of prompt boundaries:

**Weaknesses:**
- No detection of control characters or escape sequences
- No filtering of JSON injection attempts in prompts
- No handling of markdown or formatting that could alter prompt structure
- No sanitization of special characters that could modify instruction behavior
- No neutralization of attempts to inject system-level commands

### Security Testing for AI Input Vectors
**Score: 1/10**

There is no evidence of security testing for AI-specific input vectors:

**Weaknesses:**
- No test cases for prompt injection scenarios
- No fuzzing or boundary testing for AI inputs
- No security regression testing for AI features
- No automated security scanning for AI vulnerabilities
- No continuous validation of AI input protection mechanisms

---

## Content Safety & Moderation

### AI-Generated Content Filtering and Moderation
**Score: 1/10**

There is no implementation of content filtering or moderation for AI-generated outputs:

**Weaknesses:**
- No filtering of potentially harmful AI-generated content
- No classification of AI responses by safety level
- No content moderation pipeline for AI outputs
- No validation of AI-generated content against safety policies
- No post-processing to ensure safe content delivery

### Inappropriate Content Detection Systems
**Score: 1/10**

The application lacks systems to detect inappropriate content in AI responses:

**Weaknesses:**
- No detection of harmful, offensive, or explicit content
- No classification of content sensitivity levels
- No flagging system for potentially problematic AI outputs
- No integration with content moderation APIs or services
- No keyword or pattern matching for inappropriate content

### Bias Detection and Mitigation Strategies
**Score: 2/10**

The application includes a basic sentiment analysis but no comprehensive bias detection:

```python
# From src/models/analysis_models.py
class Sentiment(BaseModel):
    label: Literal["positive", "neutral", "negative"]
    score: float = Field(..., ge=-1.0, le=1.0)
    rationale: str
    evidence: List[str] = Field(default_factory=list)
```

**Strengths:**
- Basic sentiment analysis with rationale and evidence
- Neutral sentiment labeling capability

**Weaknesses:**
- No detection of biased language or perspectives in AI output
- No balancing mechanisms for one-sided analyses
- No diversity and inclusion considerations in content analysis
- No mitigation strategies for detected bias
- No reporting of potential bias in analysis results

### Harmful Content Prevention and Blocking
**Score: 1/10**

There are no mechanisms to prevent or block harmful AI-generated content:

**Weaknesses:**
- No filtering of dangerous, illegal, or harmful AI outputs
- No blocking of content that could promote harm
- No safety thresholds or guardrails for AI responses
- No content policy enforcement mechanisms
- No fallback for potentially problematic content

### Content Classification and Rating Systems
**Score: 1/10**

The application does not implement content classification or rating systems:

**Weaknesses:**
- No classification of content by safety or sensitivity level
- No age-appropriate content filtering
- No rating system for content appropriateness
- No metadata annotations for content sensitivity
- No user-configurable filtering options

### User Reporting and Content Review Processes
**Score: 1/10**

There are no user reporting mechanisms or content review processes:

**Weaknesses:**
- No user feedback mechanism for problematic AI responses
- No reporting system for inappropriate content
- No review process for flagged content
- No continuous improvement loop based on content reports
- No moderation interface or admin controls for content

---

## Data Privacy & Protection

### PII Detection and Protection in AI Processing
**Score: 2/10**

The application lacks specific PII detection and protection mechanisms:

```python
# From src/scrapers/web_scraper.py - basic validation only
validate_url_public(url)
```

**Strengths:**
- Basic validation to ensure URLs are public (not private networks)

**Weaknesses:**
- No detection of PII in scraped content
- No redaction of sensitive information before AI processing
- No filtering of personal data in AI prompts
- No privacy analysis of AI outputs for data leakage
- No data minimization practices for AI processing

### Data Anonymization and Pseudonymization
**Score: 1/10**

There are no data anonymization or pseudonymization capabilities:

**Weaknesses:**
- No anonymization of user data in AI processing
- No pseudonymization of identifiers before AI analysis
- No masking of sensitive data in prompts or responses
- No privacy-preserving techniques for data processing
- No data transformation to protect privacy

### Secure Data Transmission to AI Services
**Score: 4/10**

The application uses the OpenAI client library which provides secure transmission:

```python
# From src/providers/llm_client.py
def __init__(self):
    if not settings.OPENAI_API_KEY:
        raise RuntimeError("OPENAI_API_KEY not set")
    self.client = OpenAI(api_key=settings.OPENAI_API_KEY)
```

**Strengths:**
- Standard OpenAI client which uses HTTPS for transmission
- API key authentication for secure access

**Weaknesses:**
- No additional encryption for sensitive data
- No secure header configuration beyond defaults
- No certificate pinning or additional verification
- No transmission monitoring for sensitive data
- No data leakage prevention in transit

### Data Retention and Deletion Policies
**Score: 1/10**

There are no apparent data retention or deletion policies:

**Weaknesses:**
- No data retention policies for AI requests and responses
- No automatic deletion of sensitive processed data
- No user controls for data retention preferences
- No implementation of data lifecycle management
- No documentation of upstream provider data retention

### Consent Management for AI Processing
**Score: 1/10**

The application has no consent management for AI processing:

**Weaknesses:**
- No user consent collection for AI analysis
- No opt-out mechanisms for AI processing
- No transparency about AI usage and data processing
- No preference management for AI features
- No consent records or documentation

### GDPR and Privacy Regulation Compliance
**Score: 2/10**

There is minimal consideration of privacy regulations:

```python
# From src/api/middleware.py - only basic error handling
@app.exception_handler(SSRFBlockedError)
async def ssrf_handler(_, exc: SSRFBlockedError):
    return JSONResponse(status_code=403, content={"detail": str(exc)})
```

**Strengths:**
- Some basic error handling for security-related exceptions

**Weaknesses:**
- No comprehensive GDPR compliance mechanisms
- No data subject rights implementation
- No privacy impact assessment for AI features
- No documentation of compliance measures
- No international data protection considerations

---

## Access Control & Authorization

### Role-Based Access Control for AI Features
**Score: 1/10**

There is no implementation of role-based access control for AI features:

**Weaknesses:**
- No user role definition for AI feature access
- No permission checks before AI processing
- No different access levels for AI capabilities
- No administrative controls for AI features
- No least privilege implementation for AI access

### API Key and Credential Management Security
**Score: 5/10**

The application implements basic API key management:

```python
# From src/config/settings.py
class Settings(BaseSettings):
    # ...existing code...
    ENABLE_LLM_ANALYSIS: bool = False           # set True to turn on analysis
    OPENAI_API_KEY: str | None = None
    # ...existing code...
    
    model_config = SettingsConfigDict(
        env_file=".env",
        env_file_encoding="utf-8",
        extra="ignore",
    )
```

**Strengths:**
- API key stored in environment variables
- Environment file support (.env)
- Feature flag to disable AI features completely
- Validation for API key presence

**Weaknesses:**
- No key rotation mechanism
- No runtime secret management
- No access level differentiation for keys
- No monitoring of key usage or abuse
- No credential compromise detection

### User Authentication for AI Service Access
**Score: 2/10**

The application has minimal authentication for AI service access:

```python
# From src/providers/llm_client.py
def __init__(self):
    if not settings.OPENAI_API_KEY:
        raise RuntimeError("OPENAI_API_KEY not set")
    self.client = OpenAI(api_key=settings.OPENAI_API_KEY)
```

**Strengths:**
- Basic validation that API key exists

**Weaknesses:**
- No user-level authentication for AI features
- No session validation before AI access
- No identity verification for AI operations
- No granular access controls for AI capabilities
- No multi-factor authentication for sensitive AI features

### Feature-Level Authorization and Permissions
**Score: 2/10**

The application has a basic feature flag but lacks granular permissions:

```python
# From frontend/app.py - simple checkbox for AI analysis
st.checkbox(
    "Run AI Analysis", value=False, key="run_analysis",
    help="Requires OPENAI_API_KEY and ENABLE_LLM_ANALYSIS=true on backend.",
)
```

**Strengths:**
- Feature flag to enable/disable AI analysis
- User option to run or skip AI analysis

**Weaknesses:**
- No granular permission system for different AI operations
- No authorization checks for specific AI capabilities
- No user-specific permission management
- No context-aware authorization
- No progressive authorization based on sensitivity

### Audit Logging for AI Service Usage
**Score: 2/10**

The application implements basic logging but lacks comprehensive audit trails:

```python
# From src/scrapers/web_scraper.py
log = logging.getLogger(__name__)

# Example logging usage
log.warning("Fetch attempt %s failed for %s: %r", attempt + 1, url, e)
```

**Strengths:**
- Basic error logging for failed operations
- Logger initialization in some modules

**Weaknesses:**
- No specific audit logging for AI operations
- No tracking of AI feature usage
- No recording of authorization decisions
- No monitoring of unusual access patterns
- No integration with security monitoring systems

### Session Management and Timeout Handling
**Score: 3/10**

The application has basic session management in the frontend:

```python
# From frontend/app.py - simple session state management
st.session_state["last_payload"] = data
```

**Strengths:**
- Basic session state management in Streamlit
- Simple data persistence between interactions

**Weaknesses:**
- No explicit session timeout for AI features
- No re-authentication for sensitive operations
- No session validation before AI processing
- No idle session management
- No session security controls

---

## AI Service Security

### Secure Communication with AI Providers
**Score: 5/10**

The application uses the OpenAI client library which implements secure communication:

```python
# From src/providers/llm_client.py
def __init__(self):
    if not settings.OPENAI_API_KEY:
        raise RuntimeError("OPENAI_API_KEY not set")
    self.client = OpenAI(api_key=settings.OPENAI_API_KEY)
```

**Strengths:**
- Standard OpenAI client which uses HTTPS
- Timeout configuration to prevent hanging requests
- Basic error handling for response parsing

**Weaknesses:**
- No additional TLS configuration
- No certificate validation enhancements
- No secure header customization
- No network security monitoring
- No fallback for communication failures

### Certificate Validation and TLS Implementation
**Score: 3/10**

The application relies on default certificate validation:

**Strengths:**
- Default Python/OpenAI client TLS implementation
- Standard HTTPS protocol usage

**Weaknesses:**
- No explicit certificate validation configuration
- No certificate pinning implementation
- No TLS version enforcement
- No cipher suite configuration
- No certificate expiration monitoring

### API Rate Limiting and Abuse Prevention
**Score: 4/10**

The application has basic timeout settings but limited rate limiting:

```python
# From src/config/settings.py
LLM_TIMEOUT_S: int = 30

# From src/providers/llm_client.py - using the timeout
timeout=settings.LLM_TIMEOUT_S,
```

**Strengths:**
- Request timeout configuration
- OpenAI's built-in rate limits (external)

**Weaknesses:**
- No explicit rate limiting implementation
- No tracking of API usage frequency
- No throttling for excessive requests
- No abuse detection mechanisms
- No user-specific quotas or limits

### Service Monitoring for Security Incidents
**Score: 1/10**

There is no evident monitoring for AI security incidents:

**Weaknesses:**
- No monitoring of abnormal AI request patterns
- No detection of unusual AI outputs
- No alerting for security-related incidents
- No tracking of API key usage anomalies
- No integration with security monitoring systems

### Vendor Security Assessment and Compliance
**Score: 3/10**

The application uses OpenAI as its AI provider:

```python
# From src/providers/llm_client.py
from openai import OpenAI
```

**Strengths:**
- Usage of a major AI provider with security practices
- Standard API integration approach

**Weaknesses:**
- No documented vendor security assessment
- No fallback to alternative providers
- No vendor risk management approach
- No compliance verification for AI provider
- No third-party security controls

### Data Localization and Sovereignty Requirements
**Score: 1/10**

There is no implementation for data localization or sovereignty requirements:

**Weaknesses:**
- No controls for data geographical location
- No regional API endpoint selection
- No compliance with cross-border data restrictions
- No documentation of data residency considerations
- No configuration options for data sovereignty

---

## Compliance & Governance

### AI Ethics and Responsible AI Implementation
**Score: 2/10**

There is minimal implementation of AI ethics principles:

**Strengths:**
- Basic analysis rather than generation reduces some ethical risks
- Simple analytical objectives without manipulation

**Weaknesses:**
- No explicit AI ethics guidelines or policies
- No fairness considerations in AI implementation
- No oversight mechanisms for AI usage
- No responsible AI documentation
- No ethical review process for AI features

### Regulatory Compliance (AI Act, Industry Standards)
**Score: 2/10**

The application shows minimal awareness of AI regulations:

**Strengths:**
- Simple analytical use case has lower regulatory risk
- Transparent content analysis purpose

**Weaknesses:**
- No explicit AI regulatory compliance documentation
- No alignment with emerging AI regulations
- No consideration of industry standards for AI
- No compliance monitoring or reporting
- No regulatory risk assessment for AI features

### Documentation of AI Decision-Making Processes
**Score: 3/10**

The application has basic structure for AI analysis results:

```python
# From src/models/analysis_models.py
class AnalysisReport(BaseModel):
    summary: str
    key_points: List[str] = Field(default_factory=list)
    topics: List[str] = Field(default_factory=list)
    keywords: List[str] = Field(default_factory=list)
    sentiment: Sentiment
    entities: List[Entity] = Field(default_factory=list)
    word_count: int
    reading_grade: Optional[str] = None
```

**Strengths:**
- Structured analysis results with multiple components
- Sentiment analysis includes rationale and evidence
- Clear report structure for analysis output

**Weaknesses:**
- No explanation of AI decision-making process
- No confidence scores for different analysis elements
- No documentation of AI limitations or potential errors
- No audit trail of AI processing steps
- No version tracking for AI models or prompts

### Risk Assessment and Mitigation Strategies
**Score: 2/10**

There is minimal risk assessment for AI features:

**Strengths:**
- Limited scope of AI analysis reduces some risks
- Basic error handling for API failures

**Weaknesses:**
- No formal AI risk assessment methodology
- No identification of specific AI-related risks
- No mitigation strategies for potential harms
- No impact assessment for different stakeholders
- No continuous risk monitoring for AI features

### AI Governance and Oversight Mechanisms
**Score: 1/10**

The application lacks AI governance and oversight mechanisms:

**Weaknesses:**
- No AI governance framework or policies
- No oversight body or responsible individuals identified
- No regular review process for AI features
- No accountability mechanisms for AI usage
- No governance documentation for AI integration

### Transparency and Explainability Requirements
**Score: 3/10**

The application provides some transparency in AI analysis:

```python
# From src/models/analysis_models.py
class Sentiment(BaseModel):
    label: Literal["positive", "neutral", "negative"]
    score: float = Field(..., ge=-1.0, le=1.0)
    rationale: str  # why the sentiment label was chosen
    evidence: List[str] = Field(default_factory=list)
```

**Strengths:**
- Sentiment analysis includes rationale and evidence
- Clear display of AI analysis components
- User awareness of AI feature usage

**Weaknesses:**
- No detailed explanation of how analysis is generated
- No model information or limitations disclosure
- No uncertainty communication for AI results
- No explainability features for complex analyses
- No user education about AI capabilities and limitations

---

## AI Security & Safety Improvement Recommendations

### 1. Implement Robust Input Validation and Prompt Injection Detection
**Priority: HIGH**

Create a dedicated security layer for validating content before sending it to the OpenAI API:

```python
# Create a new file: src/utils/ai_security.py
import re
from typing import Tuple

class PromptSecurityValidator:
    """Validates and sanitizes content before sending to AI services."""
    
    def __init__(self):
        # Patterns that might indicate prompt injection attempts
        self.injection_patterns = [
            r"ignore (?:all )?(?:previous |above )?instructions",
            r"disregard (?:all )?(?:previous |above )?instructions",
            r"forget (?:all )?(?:previous |above )?instructions",
            r"do not follow (?:previous |above )?instructions",
            r"system prompt",
            r"as an AI language model",
            r"You are a[n]? [a-zA-Z\s]+ AI",
            r"new instructions:",
            r"<system>",
            r"\[system\]:",
        ]
        self.compiled_patterns = [re.compile(pattern, re.IGNORECASE) for pattern in self.injection_patterns]
        
        # Detect special control characters
        self.control_char_pattern = re.compile(r"[\x00-\x08\x0B\x0C\x0E-\x1F\x7F]")
        
    def validate_content(self, content: str) -> Tuple[bool, Optional[str], str]:
        """
        Validates content for potential prompt injection or security issues.
        
        Returns:
            Tuple of (is_safe, issue_description, sanitized_content)
        """
        # Check for control characters
        if self.control_char_pattern.search(content):
            sanitized = self.control_char_pattern.sub("", content)
            return False, "Control characters detected and removed", sanitized
        
        # Check for prompt injection patterns
        for i, pattern in enumerate(self.compiled_patterns):
            if pattern.search(content):
                match = pattern.search(content)
                matched_text = match.group(0) if match else "unknown pattern"
                return False, f"Potential prompt injection detected: {matched_text}", content
        
        # Check for excessive length
        if len(content) > 100000:  # Arbitrary large size
            truncated = content[:100000]
            return False, "Content excessively long, truncated to 100,000 chars", truncated
            
        return True, None, content
        
    def sanitize_system_prompt(self, prompt: str) -> str:
        """
        Ensures system prompts are secure and don't contain problematic patterns.
        Adds security boundaries to system prompts.
        """
        # Add prompt boundary markers
        return f"<system>\n{prompt}\n</system>"
        
    def sanitize_user_content(self, content: str) -> str:
        """
        Sanitizes user-generated content to make it safer for AI processing.
        """
        # Remove any attempt to inject system-level instructions
        sanitized = content
        
        # Replace any attempt to use system markers
        sanitized = re.sub(r"<system>|</system>|\[system\]|\[/system\]", "", sanitized)
        
        # Replace excessive newlines that might be used to visually separate content
        sanitized = re.sub(r"\n{3,}", "\n\n", sanitized)
        
        return sanitized
        
    def create_secure_prompt(self, system_prompt: str, user_content: str) -> Tuple[str, str]:
        """
        Creates a secure version of system prompt and user content.
        
        Returns:
            Tuple of (secure_system_prompt, secure_user_content)
        """
        secure_system = self.sanitize_system_prompt(system_prompt)
        
        # Validate and sanitize user content
        is_safe, issue, sanitized_user = self.validate_content(user_content)
        if not is_safe:
            # Log the issue but proceed with sanitized content
            print(f"Content security issue: {issue}")
        
        secure_user = self.sanitize_user_content(sanitized_user)
        
        return secure_system, secure_user
```

Integrate the prompt security validator with the LLM client:

```python
# Update src/providers/llm_client.py
from ..utils.ai_security import PromptSecurityValidator
import logging

class LLMClient:
    def __init__(self):
        if not settings.OPENAI_API_KEY:
            raise RuntimeError("OPENAI_API_KEY not set")
        self.client = OpenAI(api_key=settings.OPENAI_API_KEY)
        self.security_validator = PromptSecurityValidator()
        
    def complete_json(self, system_prompt: str, user_prompt: str) -> Dict[str, Any]:
        try:
            # Apply security validation and sanitization
            secure_system, secure_user = self.security_validator.create_secure_prompt(
                system_prompt, user_prompt
            )
            
            # Create the completion with secured prompts
            r = self.client.chat.completions.create(
                model=settings.LLM_MODEL,
                temperature=settings.LLM_TEMPERATURE,
                messages=[
                    {"role": "system", "content": secure_system},
                    {"role": "user", "content": secure_user},
                ],
                response_format={"type": "json_object"},
                timeout=settings.LLM_TIMEOUT_S,
                max_tokens=settings.LLM_MAX_OUTPUT_TOKENS,
            )
            content = r.choices[0].message.content
            
            # Parse and return the JSON response
            return json.loads(content)
        except json.JSONDecodeError as e:
            logging.error(f"Failed to parse AI response as JSON: {e}")
            return {
                "error": "Invalid AI response format",
                "summary": "The AI generated a response in an incorrect format.",
                "key_points": ["There was an error processing the AI response."]
            }
        except Exception as e:
            logging.error(f"AI request error: {str(e)}")
            return {
                "error": f"AI processing error: {str(e)}",
                "summary": "There was an error processing your content.",
                "key_points": ["The AI analysis service is currently unavailable."]
            }
```

### 2. Implement Content Safety Filtering and Moderation
**Priority: HIGH**

Create a content safety service to filter both incoming content and AI-generated responses:

```python
# Create a new file: src/utils/content_safety.py
from typing import Dict, List, Tuple, Any, Optional
import re

class ContentSafetyFilter:
    """Filters and moderates content for safety concerns."""
    
    def __init__(self):
        # Simple patterns for potentially harmful content
        # In a production system, these would be more comprehensive
        self.harmful_patterns = [
            r'\b(extreme violence|explicit content|illegal activity)\b',
            r'\b(how to (make|create) (bomb|explosive))\b',
            r'\b(child (abuse|exploitation))\b',
            r'\b(terrorist|terrorism|extremist)\b',
            # Add more patterns as needed
        ]
        self.compiled_patterns = [re.compile(pattern, re.IGNORECASE) for pattern in self.harmful_patterns]
        
        # Sensitive content patterns (should trigger warnings but not blocking)
        self.sensitive_patterns = [
            r'\b(death|suicide|self-harm)\b',
            r'\b(graphic|disturbing)\b',
            r'\b(controversial|offensive)\b',
            # Add more patterns as needed
        ]
        self.compiled_sensitive = [re.compile(pattern, re.IGNORECASE) for pattern in self.sensitive_patterns]
        
    def check_input_safety(self, content: str) -> Tuple[bool, List[str]]:
        """
        Checks if input content is safe for AI processing.
        
        Returns:
            Tuple of (is_safe, list_of_issues)
        """
        issues = []
        
        # Check for harmful patterns
        for i, pattern in enumerate(self.compiled_patterns):
            if pattern.search(content):
                match = pattern.search(content)
                matched_text = match.group(0) if match else "unknown pattern"
                issues.append(f"Potentially harmful content detected: {matched_text}")
        
        # Check for sensitive patterns
        sensitive_issues = []
        for i, pattern in enumerate(self.compiled_sensitive):
            if pattern.search(content):
                match = pattern.search(content)
                matched_text = match.group(0) if match else "unknown pattern"
                sensitive_issues.append(f"Sensitive content detected: {matched_text}")
                
        # If we have harmful content issues, content is not safe
        is_safe = len(issues) == 0
        
        # Add sensitive issues as warnings
        if sensitive_issues:
            issues.extend(sensitive_issues)
            
        return is_safe, issues
        
    def filter_ai_response(self, response: Dict[str, Any]) -> Tuple[Dict[str, Any], List[str]]:
        """
        Filters AI-generated responses for safety concerns.
        
        Returns:
            Tuple of (filtered_response, list_of_issues)
        """
        filtered_response = response.copy()
        issues = []
        
        # Check summary
        if "summary" in filtered_response:
            is_safe, summary_issues = self.check_input_safety(filtered_response["summary"])
            if not is_safe:
                filtered_response["summary"] = "[Content filtered for safety reasons]"
                issues.extend(summary_issues)
        
        # Check key points
        if "key_points" in filtered_response and isinstance(filtered_response["key_points"], list):
            safe_points = []
            for i, point in enumerate(filtered_response["key_points"]):
                if isinstance(point, str):
                    is_safe, point_issues = self.check_input_safety(point)
                    if is_safe:
                        safe_points.append(point)
                    else:
                        safe_points.append("[Content filtered for safety reasons]")
                        issues.extend([f"Key point {i+1}: {issue}" for issue in point_issues])
            filtered_response["key_points"] = safe_points
        
        # Check sentiment rationale
        if "sentiment" in filtered_response and isinstance(filtered_response["sentiment"], dict):
            sentiment = filtered_response["sentiment"]
            if "rationale" in sentiment:
                is_safe, rationale_issues = self.check_input_safety(sentiment["rationale"])
                if not is_safe:
                    filtered_response["sentiment"]["rationale"] = "[Content filtered for safety reasons]"
                    issues.extend([f"Sentiment rationale: {issue}" for issue in rationale_issues])
        
        return filtered_response, issues
```

Integrate content safety filtering with the LLM client:

```python
# Update src/providers/llm_client.py
from ..utils.content_safety import ContentSafetyFilter

class LLMClient:
    def __init__(self):
        # ...existing initialization...
        self.safety_filter = ContentSafetyFilter()
        
    def complete_json(self, system_prompt: str, user_prompt: str) -> Dict[str, Any]:
        start_time = time.time()
        safety_issues = []
        pii_detected = {}
        status = "success"
        error_msg = None
        output_length = 0
        
        try:
            # Check input safety if content filtering enabled
            if ai_security_policy.should_apply_content_filter():
                is_safe, input_safety_issues = self.safety_filter.check_input_safety(user_prompt)
                if not is_safe:
                    safety_issues.extend(input_safety_issues)
                    logging.warning(f"Safety issues detected in input: {input_safety_issues}")
                
            # Apply privacy protection if enabled
            if ai_security_policy.should_protect_pii():
                privacy_protector = PrivacyProtector()
                safe_user_prompt, pii_counts = privacy_protector.process_content_for_privacy(user_prompt)
                if pii_counts:
                    pii_detected = pii_counts
                    user_prompt = safe_user_prompt
                    ai_security_policy.log_security_event(
                        "pii_redacted", 
                        {"pii_counts": pii_counts}
                    )
                
            # Apply prompt injection protection if enabled
            if ai_security_policy.should_prevent_prompt_injection():
                secure_system, secure_user = self.security_validator.create_secure_prompt(
                    system_prompt, user_prompt
                )
                system_prompt = secure_system
                user_prompt = secure_user
            
            # Create the completion
            r = self.client.chat.completions.create(
                model=settings.LLM_MODEL,
                temperature=settings.LLM_TEMPERATURE,
                messages=[
                    {"role": "system", "content": system_prompt},
                    {"role": "user", "content": user_prompt},
                ],
                response_format={"type": "json_object"},
                timeout=settings.LLM_TIMEOUT_S,
                max_tokens=settings.LLM_MAX_OUTPUT_TOKENS,
            )
            content = r.choices[0].message.content
            output_length = len(content)
            
            # Parse the JSON response
            parsed_response = json.loads(content)
            
            # Filter the response for safety if enabled
            if ai_security_policy.should_apply_content_filter():
                filtered_response, response_issues = self.safety_filter.filter_ai_response(parsed_response)
                if response_issues:
                    safety_issues.extend(response_issues)
                    ai_security_policy.log_security_violation(
                        "unsafe_ai_response", 
                        {"issues": response_issues}
                    )
                parsed_response = filtered_response
                
            return parsed_response
            
        except Exception as e:
            status = "error"
            error_msg = str(e)
            ai_security_policy.log_security_error(
                "ai_request_error", 
                {"error": error_msg}
            )
            
            # Return error response
            error_response = {
                "error": f"AI processing error: {error_msg}",
                "summary": "There was an error processing your content.",
                "key_points": ["The AI analysis service is currently unavailable."]
            }
            output_length = len(json.dumps(error_response))
            return error_response
            
        finally:
            # Log the operation regardless of outcome
            duration_ms = (time.time() - start_time) * 1000
            
            ai_monitor.log_operation(AIOperationLog(
                timestamp=start_time,
                operation_type="content_analysis",
                model=settings.LLM_MODEL,
                input_length=len(system_prompt) + len(user_prompt),
                output_length=output_length,
                duration_ms=duration_ms,
                status=status,
                error=error_msg,
                safety_issues=safety_issues,
                pii_detected=pii_detected
            ))
```

### 3. Implement Data Privacy Protection
**Priority: MEDIUM**

Create a data privacy service to handle PII detection and protection:

```python
# Create a new file: src/utils/data_privacy.py
import re
from typing import Tuple, Dict, Any, List, Optional

class PrivacyProtector:
    """Detects and protects PII in content for AI processing."""
    
    def __init__(self):
        # PII detection patterns
        self.pii_patterns = {
            "email": r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b',
            "phone": r'\b(\+\d{1,3}[- ]?)?\(?\d{3}\)?[- ]?\d{3}[- ]?\d{4}\b',
            "ssn": r'\b\d{3}[-]?\d{2}[-]?\d{4}\b',
            "credit_card": r'\b(?:\d{4}[-\s]?){3}\d{4}\b',
            "address": r'\b\d+\s+[A-Za-z0-9\s,]+(?:Avenue|Ave|Street|St|Road|Rd|Boulevard|Blvd|Drive|Dr|Lane|Ln|Way)\b',
            "ip_address": r'\b\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}\b',
        }
        
        # Compile all patterns
        self.compiled_patterns = {
            key: re.compile(pattern, re.IGNORECASE) 
            for key, pattern in self.pii_patterns.items()
        }
        
    def detect_pii(self, text: str) -> Dict[str, List[str]]:
        """
        Detects potential PII in text.
        
        Returns:
            Dictionary mapping PII types to lists of detected instances
        """
        results = {}
        
        for pii_type, pattern in self.compiled_patterns.items():
            matches = pattern.findall(text)
            if matches:
                # Deduplicate matches
                unique_matches = list(set(matches))
                results[pii_type] = unique_matches
                
        return results
        
    def redact_pii(self, text: str) -> Tuple[str, Dict[str, int]]:
        """
        Redacts PII from text.
        
        Returns:
            Tuple of (redacted_text, count_by_pii_type)
        """
        redacted_text = text
        counts = {}
        
        for pii_type, pattern in self.compiled_patterns.items():
            matches = pattern.findall(text)
            if matches:
                unique_matches = list(set(matches))
                counts[pii_type] = len(unique_matches)
                
                for match in unique_matches:
                    redaction = f"[REDACTED {pii_type.upper()}]"
                    redacted_text = redacted_text.replace(match, redaction)
                    
        return redacted_text, counts
        
    def process_content_for_privacy(self, content: str) -> Tuple[str, Dict[str, int]]:
        """
        Process content to protect privacy before AI analysis.
        
        Returns:
            Tuple of (privacy_safe_content, pii_counts)
        """
        # Detect PII in content
        pii_detections = self.detect_pii(content)
        
        # If PII found, redact it
        if pii_detections:
            redacted_content, counts = self.redact_pii(content)
            return redacted_content, counts
            
        # No PII found
        return content, {}
```

Integrate the privacy protector with the analysis service:

```python
# Update analysis_service.py (inferred file)
from ..utils.data_privacy import PrivacyProtector
import logging

class AnalysisService:
    def __init__(self):
        self.llm_client = LLMClient()
        self.privacy_protector = PrivacyProtector()
        
    async def analyze_content(self, content: ScrapedContent) -> AnalysisReport:
        try:
            # Generate system prompt
            system_prompt = """You are an expert content analyzer specializing in extracting key insights from web content.
            Analyze the provided content objectively and extract insights including summary, key points, topics, keywords, sentiment, and entities.
            Focus only on the content provided and avoid making assumptions beyond what is explicitly stated.
            Provide your analysis in a structured JSON format."""
            
            # Apply privacy protection to content before AI processing
            privacy_safe_text, pii_counts = self.privacy_protector.process_content_for_privacy(content.main_text)
            
            # Log if PII was detected and redacted
            if pii_counts:
                logging.info(f"PII detected and redacted from content: {pii_counts}")
            
            # Generate user prompt with privacy-protected content
            user_prompt = f"""Analyze the following web content from {content.url}:
            
            Title: {content.title or ""}
            
            Content:
            {privacy_safe_text[:settings.LLM_MAX_INPUT_TOKENS // 4]}  # Approximate token limit
            
            Provide a comprehensive analysis including summary, key points, topics, keywords, sentiment analysis, and entity recognition.
            """
            
            # Get analysis from LLM
            analysis_data = self.llm_client.complete_json(system_prompt, user_prompt)
            
            # Convert to AnalysisReport
            return AnalysisReport(**analysis_data)
            
        except Exception as e:
            logging.error(f"Analysis failed: {str(e)}")
            raise
```

### 4. Add Comprehensive Error Handling and Monitoring
**Priority: MEDIUM**

Create a monitoring and error handling service for AI operations:

```python
# Create a new file: src/utils/ai_monitoring.py
import time
import json
import logging
from typing import Dict, Any, Optional, List
from dataclasses import dataclass
from datetime import datetime

@dataclass
class AIOperationLog:
    """Represents a log entry for an AI operation."""
    timestamp: float
    operation_type: str
    model: str
    input_length: int
    output_length: int
    duration_ms: float
    status: str
    error: Optional[str] = None
    safety_issues: List[str] = None
    pii_detected: Dict[str, int] = None
    
    def to_dict(self) -> Dict[str, Any]:
        """Convert to dictionary for serialization."""
        return {
            "timestamp": self.timestamp,
            "time": datetime.fromtimestamp(self.timestamp).isoformat(),
            "operation_type": self.operation_type,
            "model": self.model,
            "input_length": self.input_length,
            "output_length": self.output_length,
            "duration_ms": self.duration_ms,
            "status": self.status,
            "error": self.error,
            "safety_issues": self.safety_issues or [],
            "pii_detected": self.pii_detected or {},
        }

class AIMonitor:
    """Monitors AI operations for security, performance, and compliance."""
    
    def __init__(self, log_file: Optional[str] = None):
        self.logger = logging.getLogger("ai_monitor")
        self.operations: List[AIOperationLog] = []
        self.max_operations = 1000  # Keep last 1000 operations in memory
        self.log_file = log_file
        
        # Configure file handler if log file specified
        if log_file:
            file_handler = logging.FileHandler(log_file)
            file_handler.setFormatter(logging.Formatter('%(asctime)s - %(levelname)s - %(message)s'))
            self.logger.addHandler(file_handler)
            
    def log_operation(self, operation: AIOperationLog) -> None:
        """Log an AI operation and store in history."""
        # Add to in-memory history
        self.operations.append(operation)
        
        # Trim history if too large
        if len(self.operations) > self.max_operations:
            self.operations = self.operations[-self.max_operations:]
            
        # Log to file if configured
        if self.log_file:
            self.logger.info(json.dumps(operation.to_dict()))
            
        # Log errors and safety issues at appropriate levels
        if operation.status == "error":
            self.logger.error(f"AI operation error: {operation.error}")
            
        if operation.safety_issues:
            self.logger.warning(f"AI operation safety issues: {operation.safety_issues}")
            
        if operation.pii_detected:
            self.logger.info(f"PII detected and handled: {operation.pii_detected}")
            
    def get_recent_operations(self, limit: int = 50) -> List[Dict[str, Any]]:
        """Get recent operations as dictionaries."""
        recent = self.operations[-limit:] if self.operations else []
        return [op.to_dict() for op in recent]
        
    def get_statistics(self) -> Dict[str, Any]:
        """Get statistics about AI operations."""
        if not self.operations:
            return {
                "total_operations": 0,
                "error_rate": 0,
                "avg_duration_ms": 0,
                "safety_issue_rate": 0,
                "pii_detection_rate": 0
            }
            
        total = len(self.operations)
        errors = sum(1 for op in self.operations if op.status == "error")
        safety_issues = sum(1 for op in self.operations if op.safety_issues)
        pii_detections = sum(1 for op in self.operations if op.pii_detected)
        
        return {
            "total_operations": total,
            "error_rate": errors / total if total > 0 else 0,
            "avg_duration_ms": sum(op.duration_ms for op in self.operations) / total if total > 0 else 0,
            "safety_issue_rate": safety_issues / total if total > 0 else 0,
            "pii_detection_rate": pii_detections / total if total > 0 else 0,
            "operations_by_status": {
                "success": total - errors,
                "error": errors
            }
        }

# Create global monitor instance
ai_monitor = AIMonitor(log_file="ai_operations.log")
```

Update the LLM client to integrate monitoring:

```python
# Update src/providers/llm_client.py
from ..utils.ai_monitoring import ai_monitor, AIOperationLog
import time

class LLMClient:
    def __init__(self):
        # ...existing initialization...
        
    def complete_json(self, system_prompt: str, user_prompt: str) -> Dict[str, Any]:
        start_time = time.time()
        safety_issues = []
        pii_detected = {}
        status = "success"
        error_msg = None
        output_length = 0
        
        try:
            # Check input safety if content filtering enabled
            if ai_security_policy.should_apply_content_filter():
                is_safe, input_safety_issues = self.safety_filter.check_input_safety(user_prompt)
                if not is_safe:
                    safety_issues.extend(input_safety_issues)
                    logging.warning(f"Safety issues detected in input: {input_safety_issues}")
                
            # Apply privacy protection if enabled
            if ai_security_policy.should_protect_pii():
                privacy_protector = PrivacyProtector()
                safe_user_prompt, pii_counts = privacy_protector.process_content_for_privacy(user_prompt)
                if pii_counts:
                    pii_detected = pii_counts
                    user_prompt = safe_user_prompt
                    ai_security_policy.log_security_event(
                        "pii_redacted", 
                        {"pii_counts": pii_counts}
                    )
                
            # Apply prompt injection protection if enabled
            if ai_security_policy.should_prevent_prompt_injection():
                secure_system, secure_user = self.security_validator.create_secure_prompt(
                    system_prompt, user_prompt
                )
                system_prompt = secure_system
                user_prompt = secure_user
            
            # Create the completion
            r = self.client.chat.completions.create(
                model=settings.LLM_MODEL,
                temperature=settings.LLM_TEMPERATURE,
                messages=[
                    {"role": "system", "content": system_prompt},
                    {"role": "user", "content": user_prompt},
                ],
                response_format={"type": "json_object"},
                timeout=settings.LLM_TIMEOUT_S,
                max_tokens=settings.LLM_MAX_OUTPUT_TOKENS,
            )
            content = r.choices[0].message.content
            output_length = len(content)
            
            # Parse the JSON response
            parsed_response = json.loads(content)
            
            # Filter the response for safety if enabled
            if ai_security_policy.should_apply_content_filter():
                filtered_response, response_issues = self.safety_filter.filter_ai_response(parsed_response)
                if response_issues:
                    safety_issues.extend(response_issues)
                    ai_security_policy.log_security_violation(
                        "unsafe_ai_response", 
                        {"issues": response_issues}
                    )
                parsed_response = filtered_response
                
            return parsed_response
            
        except Exception as e:
            status = "error"
            error_msg = str(e)
            ai_security_policy.log_security_error(
                "ai_request_error", 
                {"error": error_msg}
            )
            
            # Return error response
            error_response = {
                "error": f"AI processing error: {error_msg}",
                "summary": "There was an error processing your content.",
                "key_points": ["The AI analysis service is currently unavailable."]
            }
            output_length = len(json.dumps(error_response))
            return error_response
            
        finally:
            # Log the operation regardless of outcome
            duration_ms = (time.time() - start_time) * 1000
            
            ai_monitor.log_operation(AIOperationLog(
                timestamp=start_time,
                operation_type="content_analysis",
                model=settings.LLM_MODEL,
                input_length=len(system_prompt) + len(user_prompt),
                output_length=output_length,
                duration_ms=duration_ms,
                status=status,
                error=error_msg,
                safety_issues=safety_issues,
                pii_detected=pii_detected
            ))
```

### 5. Add API Access Control and Rate Limiting
**Priority: HIGH**

Create an API access control middleware to control AI feature usage:

```python
# Create a new file: src/middleware/ai_access_control.py
from fastapi import Request, HTTPException
import time
from typing import Dict, Set, Optional, List
import logging

class AIRateLimiter:
    """Rate limits AI API access to prevent abuse."""
    
    def __init__(
        self, 
        requests_per_minute: int = 10,
        requests_per_hour: int = 100,
        requests_per_day: int = 1000
    ):
        self.requests_per_minute = requests_per_minute
        self.requests_per_hour = requests_per_hour  
        self.requests_per_day = requests_per_day
        
        # Track requests by client identifier (IP or API key)
        self.minute_requests: Dict[str, List[float]] = {}
        self.hour_requests: Dict[str, List[float]] = {}
        self.day_requests: Dict[str, List[float]] = {}
        
        # Track blocked clients
        self.blocked_clients: Set[str] = set()
        
    def _clean_old_requests(self, client_id: str, current_time: float) -> None:
        """Clean up old request records."""
        # Keep only requests from the last minute
        if client_id in self.minute_requests:
            self.minute_requests[client_id] = [
                t for t in self.minute_requests[client_id]
                if current_time - t < 60  # Last minute
            ]
            
        # Keep only requests from the last hour
        if client_id in self.hour_requests:
            self.hour_requests[client_id] = [
                t for t in self.hour_requests[client_id]
                if current_time - t < 3600  # Last hour
            ]
            
        # Keep only requests from the last day
        if client_id in self.day_requests:
            self.day_requests[client_id] = [
                t for t in self.day_requests[client_id]
                if current_time - t < 86400  # Last day
            ]
            
    def check_rate_limit(self, client_id: str) -> Tuple[bool, Optional[str]]:
        """
        Check if a client has exceeded rate limits.
        
        Returns:
            Tuple of (is_allowed, reason_if_blocked)
        """
        current_time = time.time()
        
        # Check if client is blocked
        if client_id in self.blocked_clients:
            return False, "Client is blocked due to previous violations"
        
        # Clean old requests first
        self._clean_old_requests(client_id, current_time)
        
        # Initialize request tracking for new clients
        if client_id not in self.minute_requests:
            self.minute_requests[client_id] = []
        if client_id not in self.hour_requests:
            self.hour_requests[client_id] = []
        if client_id not in self.day_requests:
            self.day_requests[client_id] = []
            
        # Check minute limit
        if len(self.minute_requests[client_id]) >= self.requests_per_minute:
            return False, f"Rate limit exceeded: {self.requests_per_minute} requests per minute"
            
        # Check hour limit
        if len(self.hour_requests[client_id]) >= self.requests_per_hour:
            return False, f"Rate limit exceeded: {self.requests_per_hour} requests per hour"
            
        # Check day limit
        if len(self.day_requests[client_id]) >= self.requests_per_day:
            return False, f"Rate limit exceeded: {self.requests_per_day} requests per day"
            
        # Record this request
        self.minute_requests[client_id].append(current_time)
        self.hour_requests[client_id].append(current_time)
        self.day_requests[client_id].append(current_time)
        
        return True, None
        
    def block_client(self, client_id: str) -> None:
        """Block a client from making further requests."""
        self.blocked_clients.add(client_id)
        logging.warning(f"Blocked client {client_id} from AI access")
        
    def get_client_usage(self, client_id: str) -> Dict[str, int]:
        """Get usage statistics for a client."""
        current_time = time.time()
        self._clean_old_requests(client_id, current_time)
        
        return {
            "minute_requests": len(self.minute_requests.get(client_id, [])),
            "hour_requests": len(self.hour_requests.get(client_id, [])),
            "day_requests": len(self.day_requests.get(client_id, [])),
            "is_blocked": client_id in self.blocked_clients
        }

# Create global rate limiter
ai_rate_limiter = AIRateLimiter()

async def ai_access_control_middleware(request: Request, call_next):
    """Middleware to control access to AI features."""
    # Check if this is an AI endpoint
    path = request.url.path
    if path.startswith("/analyze") and request.method == "POST":
        # Get client identifier (IP address in this simple implementation)
        client_id = request.client.host
        
        # Check rate limit
        is_allowed, reason = ai_rate_limiter.check_rate_limit(client_id)
        if not is_allowed:
            return HTTPException(status_code=429, detail=reason)
            
        # Log AI access attempt
        logging.info(f"AI access from {client_id}: {path}")
        
    # Continue with the request
    return await call_next(request)
```

Update the main application to use the AI access control middleware:

```python
# Update main.py
from src.middleware.ai_access_control import ai_access_control_middleware

app = FastAPI(title="Web Content Analyzer – M1")

# Add AI access control middleware
app.middleware("http")(ai_access_control_middleware)

# ...existing middleware and setup...
```

### 6. Implement AI Security Configuration and Policy Management
**Priority: MEDIUM**

Create a centralized AI security policy manager:

```python
# Create a new file: src/utils/ai_security_policy.py
from ..config.settings import settings
from typing import Dict, Any, Optional
import logging

class AISecurityPolicy:
    """Manages AI security policies and configurations."""
    
    def __init__(self):
        self.security_enabled = settings.AI_SECURITY_ENABLED
        self.content_filter_enabled = settings.AI_CONTENT_FILTER_ENABLED
        self.pii_protection_enabled = settings.AI_PII_PROTECTION_ENABLED
        self.prompt_injection_protection_enabled = settings.AI_PROMPT_INJECTION_PROTECTION_ENABLED
        self.max_content_length = settings.AI_MAX_CONTENT_LENGTH
        self.content_filter_sensitivity = settings.AI_CONTENT_FILTER_SENSITIVITY
        
        # Configure logging
        self.logger = logging.getLogger("ai_security")
        if settings.AI_SECURITY_LOG_FILE:
            file_handler = logging.FileHandler(settings.AI_SECURITY_LOG_FILE)
            file_handler.setFormatter(logging.Formatter('%(asctime)s - %(levelname)s - %(message)s'))
            self.logger.addHandler(file_handler)
            
    def get_security_config(self) -> Dict[str, Any]:
        """Get current security configuration."""
        return {
            "security_enabled": self.security_enabled,
            "content_filter_enabled": self.content_filter_enabled,
            "pii_protection_enabled": self.pii_protection_enabled,
            "prompt_injection_protection_enabled": self.prompt_injection_protection_enabled,
            "max_content_length": self.max_content_length,
            "content_filter_sensitivity": self.content_filter_sensitivity
        }
        
    def should_apply_content_filter(self) -> bool:
        """Determine if content filtering should be applied."""
        return self.security_enabled and self.content_filter_enabled
        
    def should_protect_pii(self) -> bool:
        """Determine if PII protection should be applied."""
        return self.security_enabled and self.pii_protection_enabled
        
    def should_prevent_prompt_injection(self) -> bool:
        """Determine if prompt injection prevention should be applied."""
        return self.security_enabled and self.prompt_injection_protection_enabled
        
    def log_security_event(self, event_type: str, details: Dict[str, Any]) -> None:
        """Log a security event."""
        if settings.AI_MONITOR_ENABLED:
            self.logger.info(f"AI Security Event - {event_type}: {details}")
            
    def log_security_violation(self, violation_type: str, details: Dict[str, Any]) -> None:
        """Log a security violation."""
        if settings.AI_MONITOR_ENABLED:
            self.logger.warning(f"AI Security Violation - {violation_type}: {details}")
            
    def log_security_error(self, error_type: str, details: Dict[str, Any]) -> None:
        """Log a security error."""
        if settings.AI_MONITOR_ENABLED:
            self.logger.error(f"AI Security Error - {error_type}: {details}")

# Create global security policy instance
ai_security_policy = AISecurityPolicy()
```

Update the LLMClient to use the security policy:

```python
# Update src/providers/llm_client.py
from ..utils.ai_security_policy import ai_security_policy

class LLMClient:
    def __init__(self):
        # ...existing initialization...
        
    def complete_json(self, system_prompt: str, user_prompt: str) -> Dict[str, Any]:
        start_time = time.time()
        safety_issues = []
        pii_detected = {}
        status = "success"
        error_msg = None
        output_length = 0
        
        try:
            # Check content length
            if len(user_prompt) > ai_security_policy.max_content_length:
                user_prompt = user_prompt[:ai_security_policy.max_content_length]
                ai_security_policy.log_security_event(
                    "content_truncated", 
                    {"original_length": len(user_prompt), "truncated_to": ai_security_policy.max_content_length}
                )
            
            # Check input safety if content filtering enabled
            if ai_security_policy.should_apply_content_filter():
                is_safe, input_safety_issues = self.safety_filter.check_input_safety(user_prompt)
                if not is_safe:
                    safety_issues.extend(input_safety_issues)
                    logging.warning(f"Safety issues detected in input: {input_safety_issues}")
                
            # Apply privacy protection if enabled
            if ai_security_policy.should_protect_pii():
                privacy_protector = PrivacyProtector()
                safe_user_prompt, pii_counts = privacy_protector.process_content_for_privacy(user_prompt)
                if pii_counts:
                    pii_detected = pii_counts
                    user_prompt = safe_user_prompt
                    ai_security_policy.log_security_event(
                        "pii_redacted", 
                        {"pii_counts": pii_counts}
                    )
                
            # Apply prompt injection protection if enabled
            if ai_security_policy.should_prevent_prompt_injection():
                secure_system, secure_user = self.security_validator.create_secure_prompt(
                    system_prompt, user_prompt
                )
                system_prompt = secure_system
                user_prompt = secure_user
            
            # Create the completion
            r = self.client.chat.completions.create(
                model=settings.LLM_MODEL,
                temperature=settings.LLM_TEMPERATURE,
                messages=[
                    {"role": "system", "content": system_prompt},
                    {"role": "user", "content": user_prompt},
                ],
                response_format={"type": "json_object"},
                timeout=settings.LLM_TIMEOUT_S,
                max_tokens=settings.LLM_MAX_OUTPUT_TOKENS,
            )
            content = r.choices[0].message.content
            output_length = len(content)
            
            # Parse the JSON response
            parsed_response = json.loads(content)
            
            # Filter the response for safety if enabled
            if ai_security_policy.should_apply_content_filter():
                filtered_response, response_issues = self.safety_filter.filter_ai_response(parsed_response)
                if response_issues:
                    safety_issues.extend(response_issues)
                    ai_security_policy.log_security_violation(
                        "unsafe_ai_response", 
                        {"issues": response_issues}
                    )
                parsed_response = filtered_response
                
            return parsed_response
            
        except Exception as e:
            status = "error"
            error_msg = str(e)
            ai_security_policy.log_security_error(
                "ai_request_error", 
                {"error": error_msg}
            )
            
            # Return error response
            error_response = {
                "error": f"AI processing error: {error_msg}",
                "summary": "There was an error processing your content.",
                "key_points": ["The AI analysis service is currently unavailable."]
            }
            output_length = len(json.dumps(error_response))
            return error_response
            
        finally:
            # Log the operation regardless of outcome
            duration_ms = (time.time() - start_time) * 1000
            
            ai_monitor.log_operation(AIOperationLog(
                timestamp=start_time,
                operation_type="content_analysis",
                model=settings.LLM_MODEL,
                input_length=len(system_prompt) + len(user_prompt),
                output_length=output_length,
                duration_ms=duration_ms,
                status=status,
                error=error_msg,
                safety_issues=safety_issues,
                pii_detected=pii_detected
            ))
```

## AI Security & Safety Improvement Summary

The Web Content Analyzer application requires significant improvements to its AI security and safety implementation. The following enhancements should be prioritized:

1. **Implement Prompt Injection Protection**: Add dedicated validation to detect and neutralize attempts to manipulate AI behavior through prompt injection, ensuring system prompts remain isolated from user content.

2. **Add Content Safety Filtering**: Implement content filtering for both input and AI-generated content to detect and handle potentially harmful, offensive, or inappropriate content.

3. **Implement Data Privacy Protection**: Add PII detection and redaction to protect sensitive personal information before AI processing, ensuring compliance with privacy regulations.

4. **Add Comprehensive Monitoring and Logging**: Implement a dedicated monitoring system for AI operations to track security events, detect unusual patterns, and maintain audit trails of AI usage.

5. **Implement API Rate Limiting and Access Control**: Add rate limiting and access control to prevent abuse of AI features, ensuring fair usage and protecting against potential misuse.

6. **Create Security Policy Management**: Implement a centralized security policy system to manage AI security settings, enabling flexible configuration and consistent policy enforcement.

These improvements will significantly enhance the security and safety of the AI integration, protecting against common threats while ensuring responsible AI usage. Most implementations can leverage existing libraries and frameworks with minimal custom code, providing a high security return on development investment.