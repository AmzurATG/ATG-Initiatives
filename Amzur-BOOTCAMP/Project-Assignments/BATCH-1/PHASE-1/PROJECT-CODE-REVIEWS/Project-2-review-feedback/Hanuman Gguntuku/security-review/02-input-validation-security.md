# Input Validation & Injection Security Review

## Project Information
- **Project Title:** Web Content Analyzer
- **Review Date:** 2024-02-20
- **Reviewer:** Github Copilot

## Overall Assessment
**Score: 3/10 - POOR**

The codebase shows significant vulnerabilities in input validation and injection prevention. While some basic validation exists, there are critical gaps that need immediate attention.

## Key Findings

### 1. SQL Injection Vulnerabilities
**Risk Level: HIGH (8/10)**

#### Current Implementation:
```python
# Unsafe query construction in backend/src/services/analysis_service.py
async def get_analysis_by_url(url: str):
    # VULNERABILITY: Direct string interpolation
    query = f"SELECT * FROM analysis WHERE url = '{url}'"
    return await database.fetch_one(query)
```

#### Recommended Implementation:
```python
from sqlalchemy import select
from .models import Analysis

async def get_analysis_by_url(url: str):
    query = select(Analysis).where(Analysis.url == url)
    return await database.fetch_one(query)
```

### 2. Cross-Site Scripting (XSS)
**Risk Level: CRITICAL (9/10)**

#### Current Issues:
```typescript
// Unsafe content rendering in frontend/src/components/results_display.py
const ResultDisplay = ({ content }) => {
    // VULNERABILITY: Direct HTML rendering
    return <div dangerouslySetInnerHTML={{ __html: content }} />;
};

// Recommended Implementation:
import DOMPurify from 'dompurify';

const ResultDisplay = ({ content }) => {
    const sanitizedContent = DOMPurify.sanitize(content);
    return <div dangerouslySetInnerHTML={{ __html: sanitizedContent }} />;
};
```

### 3. Input Validation Framework
**Risk Level: HIGH (7/10)**

#### Required Implementation:
```python
from pydantic import BaseModel, HttpUrl, validator
from typing import Optional

class URLAnalysisRequest(BaseModel):
    url: HttpUrl
    depth: Optional[int] = 1
    
    @validator('depth')
    def validate_depth(cls, v: int) -> int:
        if v < 1 or v > 5:
            raise ValueError("Depth must be between 1 and 5")
        return v
    
    @validator('url')
    def validate_url(cls, v: str) -> str:
        if len(str(v)) > 2048:
            raise ValueError("URL too long")
        if not is_allowed_domain(str(v)):
            raise ValueError("Domain not allowed")
        return v
```

### 4. Command Injection Prevention
**Risk Level: CRITICAL (9/10)**

#### Current Vulnerabilities:
```python
# Unsafe command execution in backend/src/processors/content_processor.py
async def process_file(filename: str):
    # VULNERABILITY: Shell injection possible
    command = f"pdftotext {filename} -"
    result = subprocess.check_output(command, shell=True)
    
# Recommended Implementation:
from shlex import quote
async def process_file(filename: str):
    safe_filename = quote(filename)
    args = ["pdftotext", safe_filename, "-"]
    result = subprocess.check_output(args, shell=False)
```

## Security Recommendations

### 1. Immediate Actions (Priority: CRITICAL)
1. Implement Pydantic models for all API requests
2. Add content sanitization middleware
3. Replace string queries with parameterized ones
4. Add input length restrictions

```python
# Add to backend/src/middleware/security.py
from fastapi import Request
from bs4 import BeautifulSoup

async def sanitize_content_middleware(request: Request, call_next):
    if request.method in ("POST", "PUT"):
        body = await request.json()
        if "content" in body:
            body["content"] = BeautifulSoup(body["content"], "html.parser").get_text()
        request._body = json.dumps(body).encode()
    response = await call_next(request)
    return response
```

### 2. Validation Framework Setup
1. Create centralized validation module
2. Implement custom validators
3. Add validation middleware
4. Setup error handling

```python
# Add to backend/src/utils/validators.py
from typing import Any
from pydantic import BaseModel, validator

class BaseRequest(BaseModel):
    """Base model for all API requests with common validation."""
    
    @validator("*", pre=True)
    def prevent_script_injection(cls, v: Any) -> Any:
        """Prevent script injection in string fields."""
        if isinstance(v, str):
            if "<script" in v.lower():
                raise ValueError("Script tags not allowed")
        return v
```

### 3. Frontend Validation
1. Add form validation hooks
2. Implement content sanitization
3. Add length restrictions
4. Validate file uploads

```typescript
// Add to frontend/src/hooks/useValidation.ts
import { useState } from 'react';
import DOMPurify from 'dompurify';

export const useInputValidation = (initialValue = '') => {
    const [value, setValue] = useState(initialValue);
    const [error, setError] = useState<string | null>(null);
    
    const validate = (input: string): boolean => {
        if (input.length > 2048) {
            setError('Input too long');
            return false;
        }
        if (/<script/i.test(input)) {
            setError('Invalid input');
            return false;
        }
        return true;
    };
    
    const handleChange = (input: string) => {
        const sanitized = DOMPurify.sanitize(input);
        if (validate(sanitized)) {
            setValue(sanitized);
            setError(null);
        }
    };
    
    return { value, error, handleChange };
};
```

## Security Impact Matrix

### Vulnerability