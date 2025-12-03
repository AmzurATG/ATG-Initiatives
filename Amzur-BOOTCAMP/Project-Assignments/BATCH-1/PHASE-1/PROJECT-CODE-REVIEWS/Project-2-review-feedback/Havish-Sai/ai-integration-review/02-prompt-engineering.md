# Prompt Engineering & Management Review: Web Content Analyzer

## Project Information
- **Project Title:** Web Content Analyzer  
- **Name:** Havish Sai
- **Review Date:** August 7th, 2025
- **Reviewer:** Github Copilot


## Executive Summary

This review examines the prompt engineering techniques and AI input design patterns implemented in the Web Content Analyzer application. The application uses OpenAI's API to analyze scraped web content, and while there are no explicitly defined prompt templates visible in the provided code, we can infer prompt construction from the application's design and models.

**Prompt Engineering Quality Score: 5/10**

The application implements basic prompt engineering patterns with clear role definition and context provision. However, it lacks formal prompt template management, dynamic prompt optimization, and advanced context management strategies. The prompts are likely effective for their intended purpose but have significant room for improvement in structure, organization, and optimization.

---

## Prompt Design Quality & Effectiveness

### Prompt Structure and Clarity
**Score: 6/10**

Based on the codebase structure and models, we can infer the general prompt structure:

```python
# Inferred prompt structure from models and LLMClient usage
system_prompt = "You are an expert content analyzer. Analyze the provided web content..."
user_prompt = f"""Analyze the following web content from {content.url}:
Title: {content.title}
Content: {content.main_text[:token_limit]}
"""
```

**Strengths:**
- Clear role definition ("expert content analyzer")
- Structured content presentation (URL, title, content)
- Includes essential context for analysis
- Focused task description

**Weaknesses:**
- No explicit prompt templates or standardization
- Limited instruction specificity
- No few-shot examples for learning
- Minimal output format guidance beyond JSON
- No explicit tone or style guidance

### Instruction Specificity and Precision
**Score: 5/10**

The analysis models suggest specific output requirements:

```python
# From src/models/analysis_models.py - these models imply specific prompt instructions
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
- Clear output structure requirements through models
- Specific fields for different analysis aspects
- Type definitions for expected outputs
- Required vs optional field distinction

**Weaknesses:**
- No explicit guidelines for field generation
- Missing criteria for sentiment analysis
- No specificity around key point extraction logic
- Limited guidance on entity recognition scope
- Missing instructions for reading grade and word count estimation

### Context Provision and Relevance
**Score: 6/10**

The application provides relevant context for AI analysis:

```python
# Inferred context provision strategy
user_prompt = f"""Analyze the following web content from {content.url}:
Title: {content.title}
Content: {content.main_text[:max_chars]}
"""
```

**Strengths:**
- Includes source URL for context
- Provides document title for framing
- Includes main content for analysis
- Truncates content to fit token limits

**Weaknesses:**
- No hierarchical context organization
- Doesn't highlight important document sections
- No metadata or additional context
- Limited handling of multi-page content context
- No content prioritization strategy

### Example Usage and Few-Shot Learning
**Score: 2/10**

No evidence of few-shot learning examples in the code:

**Weaknesses:**
- No examples of expected outputs
- No demonstration of analysis style
- No few-shot examples for edge cases
- Missing calibration examples
- No performance comparison examples

### Role Definition and Persona Establishment
**Score: 7/10**

```python
# Inferred system prompt from design
system_prompt = """You are an expert content analyzer. 
Analyze the provided web content and extract key insights including summary, 
key points, topics, sentiment, and entities..."""
```

**Strengths:**
- Clear expert role definition
- Task-appropriate persona
- Authoritative but neutral tone
- Domain-specific expertise clarification

**Weaknesses:**
- Limited persona development
- No specification of analysis style
- Missing context on analysis purpose
- Limited guidance on output voice

### Output Format Specification and Constraints
**Score: 7/10**

The LLMClient enforces JSON output format:

```python
# From src/providers/llm_client.py
def complete_json(self, system_prompt: str, user_prompt: str) -> Dict[str, Any]:
    # ...
    response_format={"type": "json_object"},
    # ...
    content = r.choices[0].message.content
    return json.loads(content)
```

**Strengths:**
- Clear JSON output format requirement
- Enforced by API parameter
- Structured return format
- Explicit parsing of JSON response

**Weaknesses:**
- No schema validation for response
- Limited formatting guidance within fields
- No examples of expected JSON structure
- No handling of malformed responses

---

## Dynamic Prompt Construction

### Context-Aware Prompt Generation
**Score: 4/10**

The application likely uses basic dynamic content inclusion:

```python
# Inferred dynamic prompt construction
user_prompt = f"""Analyze the following web content from {content.url}:
Title: {content.title}
Content: {content.main_text[:max_chars]}
"""
```

**Strengths:**
- Dynamic inclusion of URL and title
- Content length adaptation
- Basic context-aware prompt building

**Weaknesses:**
- No adaptation based on content type
- Limited content-specific instruction modification
- Missing domain-specific prompt adjustments
- No adaptive prompt strategies
- Lack of context enrichment

### Web Content Integration and Sanitization
**Score: 5/10**

Content preparation appears to use text extraction and normalization:

```python
# Content cleaning is implemented in processors before AI consumption
# From src/processors/content_cleaner.py
def normalize_text(s: str) -> str:
    s = re.sub(r"\r\n?", "\n", s)
    s = re.sub(r"\n{3,}", "\n\n", s)
    s = re.sub(r"\s{2,}", " ", s)
    return s.strip()

# From src/processors/text_normalizer.py
def normalize_whitespace(s: str) -> str:
    if not s:
        return ""
    # Normalize CRLF/CR -> LF; collapse multi-space; strip ends
    s = s.replace("\r\n", "\n").replace("\r", "\n")
    s = _WS_MULTI.sub(" ", s)
    return s.strip()
```

**Strengths:**
- Basic text normalization
- Newline handling
- Whitespace normalization
- Consistent text preparation

**Weaknesses:**
- Limited content sanitization
- No handling of problematic content
- Missing removal of irrelevant elements
- Lack of content type-specific handling
- Limited semantic content preparation

### Personalization and Customization Strategies
**Score: 2/10**

No evidence of personalization in the prompts:

**Weaknesses:**
- No user preference incorporation
- No customization options for analysis
- No personalized analysis focus
- One-size-fits-all prompt strategy
- Missing adaptive analysis depth

### Content Chunking and Processing
**Score: 5/10**

The application implements basic content length limits:

```python
# From src/config/settings.py
LLM_MAX_INPUT_TOKENS: int = 6000

# Inferred token limiting implementation
content_excerpt = content.main_text[:max_chars]
```

**Strengths:**
- Token limit awareness
- Simple truncation to fit limits
- Configuration-driven token budgeting

**Weaknesses:**
- Simple truncation rather than semantic chunking
- No advanced content splitting strategies
- Missing summarization for large content
- No handling of content exceeding limits
- Limited token optimization

### Multi-Turn Dialogue Handling
**Score: 2/10**

The application appears to use single-turn interactions only:

```python
# From src/providers/llm_client.py
def complete_json(self, system_prompt: str, user_prompt: str) -> Dict[str, Any]:
    r = self.client.chat.completions.create(
        # ...
        messages=[
            {"role": "system", "content": system_prompt},
            {"role": "user", "content": user_prompt},
        ],
        # ...
    )
```

**Weaknesses:**
- No multi-turn conversation capability
- Single request/response model only
- No conversation history management
- No follow-up question handling
- Missing interactive refinement

### Context Window Optimization
**Score: 4/10**

Basic content length limitation is implemented:

```python
# From src/config/settings.py
LLM_MAX_INPUT_TOKENS: int = 6000
LLM_MAX_OUTPUT_TOKENS: int = 800

# Inferred implementation
content_excerpt = content.main_text[:max_chars]  # Simple character limit as proxy for tokens
```

**Strengths:**
- Explicit token limits for input and output
- Configuration-driven constraints
- Awareness of context window limitations

**Weaknesses:**
- Simplistic character counting rather than token counting
- No tracking of token usage
- Missing token budget optimization
- Limited model parameter optimization
- No task-specific model selection

---

## Prompt Template Management

### Template Organization and Versioning
**Score: 2/10**

No evidence of formal prompt template organization:

**Weaknesses:**
- No template storage system
- No versioning of prompts
- Likely hardcoded prompts in service layer
- No template management strategy
- Missing prompt history tracking

### Reusable Prompt Components and Modules
**Score: 2/10**

No reusable prompt component system visible:

**Weaknesses:**
- No modular prompt components
- No shared prompt libraries
- No template inheritance
- Missing prompt composition patterns
- No reusable prompt section management

### Template Parameterization and Customization
**Score: 4/10**

Basic string interpolation appears to be used for prompts:

```python
# Inferred template parameterization
user_prompt = f"""Analyze the following web content from {content.url}:
Title: {content.title}
Content: {content.main_text[:max_chars]}
"""
```

**Strengths:**
- Simple parameter interpolation
- Dynamic content inclusion
- Basic customization of prompts

**Weaknesses:**
- Limited parameterization beyond content
- No conditional sections or logic
- Missing advanced templating features
- No parameter validation
- No formal template engine utilization

### A/B Testing Framework for Prompts
**Score: 1/10**

No evidence of prompt testing framework:

**Weaknesses:**
- No A/B testing capability
- No prompt performance comparison
- No experimental prompt variants
- Missing quality measurement
- No prompt optimization framework

### Prompt Performance Tracking and Optimization
**Score: 2/10**

No visible prompt performance tracking:

**Weaknesses:**
- No prompt effectiveness metrics
- No response quality tracking
- No optimization feedback loop
- Missing performance comparison
- No systematic prompt improvement process

### Template Documentation and Maintenance
**Score: 2/10**

No evident prompt documentation system:

**Weaknesses:**
- No prompt documentation
- No design decisions documentation
- Missing usage guidelines
- No maintenance history
- No best practices documentation

---

## Context Management & Information Flow

### Web Content Preparation for AI Analysis
**Score: 6/10**

The application extracts and processes content before analysis:

```python
# From src/scrapers/content_extractor.py
def _extract_from_html(html: bytes, base_url: Optional[str]) -> Tuple[str, Dict[str, str], List[str], str, List[str], List[str]]:
    # Try lxml, fallback to html5lib for messy pages
    soup = BeautifulSoup(html, "lxml")
    if not soup or not soup.find():
        soup = BeautifulSoup(html, "html5lib")

    for sel in REMOVE_SELECTORS:
        for tag in soup.select(sel):
            tag.decompose()
            
    # ... extracts title, meta, headings, main_text, links, images ...
```

**Strengths:**
- Effective content extraction from HTML
- Removal of non-content elements
- Fallback parser for robustness
- Structured content organization
- Multiple content aspects extracted (title, meta, text)

**Weaknesses:**
- Limited semantic understanding of content
- No advanced NLP pre-processing
- Missing content summarization for large pages
- No handling of multi-format content
- Limited preparation for AI consumption

### Context Persistence and Retrieval
**Score: 3/10**

The application maintains context within a single request flow:

```python
# Context is passed through the request flow without persistence
# Context might be preserved in the frontend using session state:
st.session_state["last_payload"] = data
```

**Strengths:**
- Basic request-level context maintenance
- Frontend session state for results

**Weaknesses:**
- No long-term context storage
- No historical context persistence
- Missing user-specific context
- No cross-request context preservation
- Limited context retrieval capabilities

### Memory Management for Large Content
**Score: 5/10**

The application implements basic memory constraints:

```python
# From src/config/settings.py
MAX_HTML_BYTES: int = 2_500_000  # ~2.5MB per page
LLM_MAX_INPUT_TOKENS: int = 6000  # Input token limit
```

**Strengths:**
- Explicit memory constraints
- Content size limits
- Token budget management

**Weaknesses:**
- Simple truncation rather than summarization
- No advanced memory management strategies
- Missing multi-part content handling
- No streaming for large content
- Limited semantic content compression

### Context Summarization and Compression
**Score: 3/10**

No explicit context summarization visible:

**Weaknesses:**
- No content summarization techniques
- Missing semantic compression
- No hierarchical content organization
- Limited information density
- Missing context optimization

### Content Processing Flow
**Score: 5/10**

Content flows through a processing pipeline:

```python
# Inferred content processing flow:
# 1. Fetch HTML content (web_scraper.fetch)
# 2. Extract structured content (content_extractor.extract)
# 3. Process and normalize text (text_normalizer, text_processor)
# 4. Prepare AI input (truncate to token limit)
# 5. Send to OpenAI API (llm_client.complete_json)
# 6. Parse and return structured analysis
```

**Strengths:**
- Clear processing stages
- Logical content transformation flow
- Structured pipeline approach
- Separation of extraction and analysis

**Weaknesses:**
- Limited feedback loops
- No adaptive processing
- Missing parallel processing options
- Fixed linear flow without branches
- No optimization of processing order

### Session Management and Cleanup
**Score: 4/10**

Basic cleanup of resources is implemented:

```python
# From src/scrapers/web_scraper.py
async def aclose(self):
    await self._client.aclose()
```

**Strengths:**
- Client resource cleanup
- Explicit connection closure

**Weaknesses:**
- Limited session management
- No comprehensive cleanup strategy
- Missing temporary resource tracking
- No context disposal pattern
- Limited session state management

---

## Token Optimization & Cost Management

### Token Counting and Estimation
**Score: 3/10**

The application uses character limits as proxy for tokens:

```python
# From src/config/settings.py
LLM_MAX_INPUT_TOKENS: int = 6000
LLM_MAX_OUTPUT_TOKENS: int = 800

# Inferred token limiting implementation
content_excerpt = content.main_text[:max_chars]  # Characters as proxy for tokens
```

**Strengths:**
- Token limit awareness
- Configuration-driven constraints

**Weaknesses:**
- No actual token counting implementation
- Lacks use of tiktoken for accurate counts
- Simple character limits as proxy
- No tracking of token usage
- Missing token budget optimization

### Prompt Length Optimization Techniques
**Score: 4/10**

The application implements basic content truncation:

```python
# Inferred content truncation
content_excerpt = content.main_text[:max_chars]
```

**Strengths:**
- Simple content truncation
- Token limit awareness

**Weaknesses:**
- No semantic content summarization
- Missing content prioritization
- No information density optimization
- Limited prompt efficiency techniques
- No removal of redundant information

### Context Compression and Summarization
**Score: 2/10**

No advanced context compression techniques visible:

**Weaknesses:**
- No content summarization before sending
- Missing semantic compression
- No hierarchical content organization
- Limited information density
- Missing automated summarization

### Batch Processing for Multiple Requests
**Score: 2/10**

No evidence of batch processing for AI requests:

**Weaknesses:**
- Single request processing model
- No batching of similar analyses
- Missing request aggregation
- One-at-a-time processing
- No parallelization of similar requests

### Cost-Effective Model Selection
**Score: 7/10**

The application uses a cost-effective model choice:

```python
# From src/config/settings.py
LLM_MODEL: str = "gpt-4o-mini"
LLM_TEMPERATURE: float = 0.2
```

**Strengths:**
- Cost-effective model selection (gpt-4o-mini)
- Low temperature for deterministic responses
- Appropriate parameter selection
- Configurable model choice

**Weaknesses:**
- No dynamic model selection based on needs
- Missing model fallback options
- No token cost awareness
- Limited model parameter optimization
- No task-specific model selection

### Usage Tracking and Budget Management
**Score: 2/10**

No visible token usage tracking or budget management:

**Weaknesses:**
- No token usage monitoring
- Missing cost tracking
- No budget controls or alerts
- Absence of usage reporting
- No optimization based on cost metrics

---

## Safety & Security in Prompt Design

### Prompt Injection Prevention
**Score: 3/10**

Limited prompt injection protection visible:

**Strengths:**
- Separation of system and user prompts
- Basic content handling

**Weaknesses:**
- No explicit prompt injection prevention
- Missing input sanitization for AI prompts
- No boundary enforcement in prompts
- Limited protection against malicious content
- No detection of prompt manipulation attempts

### Input Sanitization and Validation
**Score: 5/10**

Basic content cleaning is implemented:

```python
# From src/processors/content_cleaner.py
def normalize_text(s: str) -> str:
    s = re.sub(r"\r\n?", "\n", s)
    s = re.sub(r"\n{3,}", "\n\n", s)
    s = re.sub(r"\s{2,}", " ", s)
    return s.strip()
```

**Strengths:**
- Basic text normalization
- Whitespace handling
- Input structure standardization

**Weaknesses:**
- Limited input sanitization
- No removal of potentially harmful content
- Missing validation of input content
- No handling of malicious inputs
- Limited security focus in preparation

### Output Filtering and Content Moderation
**Score: 3/10**

No explicit output filtering or moderation:

**Weaknesses:**
- No output content moderation
- Missing harmful content filtering
- No detection of inappropriate outputs
- Limited response validation
- No content policy enforcement

### Bias Mitigation in Prompt Design
**Score: 4/10**

The system appears to use neutral analysis framing:

```python
# Inferred system prompt from design
system_prompt = """You are an expert content analyzer. 
Analyze the provided web content and extract key insights including summary, 
key points, topics, sentiment, and entities..."""
```

**Strengths:**
- Clear expert role definition
- Structured analysis focus
- Neutral tone in prompt design

**Weaknesses:**
- No explicit bias mitigation strategies
- Missing diverse perspective encouragement
- Limited awareness of potential bias areas
- No fairness guidelines in prompts
- Missing bias detection in outputs

### Privacy Protection in Context Sharing
**Score: 4/10**

The application processes publicly accessible web content:

**Strengths:**
- Focus on public web content analysis
- No apparent sharing of user PII

**Weaknesses:**
- No explicit PII detection and handling
- Missing privacy considerations in prompts
- No data minimization in context sharing
- Limited privacy-focused preparation
- No filtering of sensitive content

### Ethical AI Usage in Prompt Construction
**Score: 4/10**

Basic ethical considerations in prompt design:

**Strengths:**
- Focused on objective content analysis
- No apparent misuse of AI capabilities

**Weaknesses:**
- No explicit ethical guidelines in prompts
- Missing responsible AI usage principles
- Limited ethical guardrails in instructions
- No guidance on potentially harmful content
- Missing transparency about AI usage

---

## Prompt Engineering Improvement Recommendations

### 1. Implement Formal Prompt Template System
**Priority: HIGH**

Create a dedicated prompt template management system to standardize prompts, enable versioning, and facilitate optimization:

```python
# Create src/prompts/template_manager.py
from typing import Dict, Any, Optional
import jinja2
import tiktoken

class PromptTemplate:
    def __init__(
        self, 
        template_id: str,
        system_template: str,
        user_template: str,
        version: str = "1.0",
        max_tokens: int = 6000
    ):
        self.template_id = template_id
        self.system_template = system_template
        self.user_template = user_template
        self.version = version
        self.max_tokens = max_tokens
        self.env = jinja2.Environment(
            trim_blocks=True,
            lstrip_blocks=True
        )
        self._system_template = self.env.from_string(system_template)
        self._user_template = self.env.from_string(user_template)
        self._tokenizer = tiktoken.encoding_for_model("gpt-4o-mini")
        
    def format(self, context: Dict[str, Any]) -> Dict[str, str]:
        """Format the templates with the given context."""
        system_prompt = self._system_template.render(**context)
        user_prompt = self._user_template.render(**context)
        
        # Count tokens to ensure we don't exceed limit
        system_tokens = len(self._tokenizer.encode(system_prompt))
        
        # Calculate remaining tokens for user content
        remaining_tokens = self.max_tokens - system_tokens - 100  # Buffer
        
        # If content needs truncation, limit it
        if 'content' in context and 'max_tokens' not in context:
            content_tokens = len(self._tokenizer.encode(context['content']))
            if content_tokens > remaining_tokens:
                # Truncate content to fit token limit
                # This is a simple approach; more sophisticated truncation could be implemented
                trunc_ratio = remaining_tokens / content_tokens
                trunc_chars = int(len(context['content']) * trunc_ratio)
                context['content'] = context['content'][:trunc_chars]
                # Re-render with truncated content
                user_prompt = self._user_template.render(**context)
        
        return {
            "system_prompt": system_prompt,
            "user_prompt": user_prompt
        }
        
    def get_token_count(self, system_prompt: str, user_prompt: str) -> int:
        """Get the token count for the formatted prompts."""
        combined = system_prompt + user_prompt
        return len(self._tokenizer.encode(combined))


# Create src/prompts/templates.py
from .template_manager import PromptTemplate

# Content Analysis Template
CONTENT_ANALYSIS_TEMPLATE = PromptTemplate(
    template_id="content_analysis",
    version="1.0",
    system_template="""You are an expert content analyzer specializing in extracting key insights from web content.
Your analysis should be objective, thorough, and well-structured.

Your task is to analyze the provided web content and extract the following elements:
1. A concise summary of the main content (3-5 sentences)
2. Key points representing the main ideas (3-8 bullet points)
3. Main topics covered in the content (1-5 topics)
4. Important keywords relevant to the content
5. Overall sentiment (positive, neutral, or negative) with supporting evidence
6. Named entities mentioned (people, organizations, locations, etc.)
7. Estimate word count and reading grade level

Format your response as a JSON object matching the specified structure.
""",
    user_template="""Analyze the following web content from {{ url }}:

{% if title %}TITLE: {{ title }}{% endif %}

{% if meta and meta.description %}
DESCRIPTION: {{ meta.description }}
{% endif %}

{% if headings %}
HEADINGS:
{% for heading in headings[:5] %}
- {{ heading }}
{% endfor %}
{% endif %}

CONTENT:
{{ content }}

Analyze this content and provide a structured analysis including summary, key points, topics, keywords, sentiment analysis, and entity recognition.
""",
    max_tokens=6000
)

# Create content-specific templates as needed
TECHNICAL_CONTENT_TEMPLATE = PromptTemplate(
    template_id="technical_content",
    version="1.0",
    system_template="""You are an expert in technical content analysis, specializing in programming, 
technology, and scientific topics. Your analysis should focus on technical accuracy, 
key technical concepts, and practical applications.
    
Your task is to analyze the provided technical content and extract:
1. A technical summary focusing on main concepts (3-5 sentences)
2. Key technical points and concepts (3-8 bullet points)
3. Technical topics covered (programming languages, frameworks, methodologies)
4. Technical terminology and keywords
5. Overall technical approach (practical, theoretical, tutorial, etc.)
6. Technical entities (technologies, methodologies, tools mentioned)
7. Code sample analysis if present
8. Technical complexity assessment (beginner, intermediate, advanced)

Format your response as a JSON object matching the specified structure.
""",
    user_template="""Analyze the following technical content from {{ url }}:

{% if title %}TITLE: {{ title }}{% endif %}

{% if meta and meta.description %}
DESCRIPTION: {{ meta.description }}
{% endif %}

{% if headings %}
HEADINGS:
{% for heading in headings[:5] %}
- {{ heading }}
{% endfor %}
{% endif %}

CONTENT:
{{ content }}

Provide a technical analysis focusing on concepts, code, and technical depth.
""",
    max_tokens=6000
)

# Create src/prompts/ab_testing.py
from typing import List, Dict, Any
import json

class ABTestPrompt:
    def __init__(self, base_prompt: str, variations: List[str]):
        self.base_prompt = base_prompt
        self.variations = variations
        
    def generate_prompts(self, context: Dict[str, Any]) -> List[str]:
        """Generate a list of prompts for A/B testing."""
        prompts = []
        
        # Base prompt
        prompts.append(self.base_prompt.format(**context))
        
        # Variations
        for variation in self.variations:
            prompts.append(variation.format(**context))
            
        return prompts

# Example usage
ab_test = ABTestPrompt(
    base_prompt="Summarize the content from {url}",
    variations=[
        "Provide a summary of the content at {url}",
        "Can you summarize the information found at {url}?"
    ]
)

context = {"url": "https://example.com"}
print(ab_test.generate_prompts(context))
```

Implement the template system in the analysis service:

```python
# Modify src/services/analysis_service.py
from src.prompts.templates import CONTENT_ANALYSIS_TEMPLATE, TECHNICAL_CONTENT_TEMPLATE
# ...

class AnalysisService:
    def __init__(self):
        self.llm_client = LLMClient()
        
    async def analyze_content(self, content: ScrapedContent) -> AnalysisReport:
        # Determine the appropriate template based on content characteristics
        template = self._select_template(content)
        
        # Prepare context for template
        context = {
            "url": content.url,
            "title": content.title or "",
            "meta": content.meta,
            "headings": content.headings,
            "content": content.main_text,
        }
        
        # Format prompts using template
        prompts = template.format(context)
        
        # Get analysis from LLM
        analysis_data = self.llm_client.complete_json(
            prompts["system_prompt"], 
            prompts["user_prompt"]
        )
        
        # Convert to AnalysisReport
        return AnalysisReport(**analysis_data)
        
    def _select_template(self, content: ScrapedContent) -> PromptTemplate:
        """Select appropriate template based on content characteristics."""
        # Simple heuristic for template selection
        technical_keywords = ["programming", "code", "developer", "software", "python", "javascript"]
        
        # Check if content is technical in nature
        content_lower = content.main_text.lower()
        if any(keyword in content_lower for keyword in technical_keywords):
            return TECHNICAL_CONTENT_TEMPLATE
            
        # Default to general content analysis
        return CONTENT_ANALYSIS_TEMPLATE
```

### 2. Implement Token Counting and Budget Management
**Priority: HIGH**

Add accurate token counting and budget management to optimize costs and prevent token limit issues:

```python
# Create src/utils/token_manager.py
import tiktoken
from typing import Dict, Optional, Tuple

class TokenManager:
    def __init__(self, model_name: str = "gpt-4o-mini"):
        self.encoding = tiktoken.encoding_for_model(model_name)
        self.model_name = model_name
        
    def count_tokens(self, text: str) -> int:
        """Count the number of tokens in a text."""
        return len(self.encoding.encode(text))
        
    def truncate_to_token_limit(self, text: str, max_tokens: int) -> str:
        """Truncate text to fit within token limit."""
        tokens = self.encoding.encode(text)
        if len(tokens) <= max_tokens:
            return text
            
        truncated_tokens = tokens[:max_tokens]
        return self.encoding.decode(truncated_tokens)
        
    def optimize_content_for_context(
        self, 
        system_prompt: str, 
        content: str, 
        max_total_tokens: int,
        token_buffer: int = 100,  # Reserve tokens for other fields
        output_tokens: int = 800  # Reserve tokens for output
    ) -> str:
        """Optimize content to fit within context window with system prompt."""
        system_tokens = self.count_tokens(system_prompt)
        available_tokens = max_total_tokens - system_tokens - token_buffer - output_tokens
        
        content_tokens = self.count_tokens(content)
        if content_tokens <= available_tokens:
            return content
            
        return self.truncate_to_token_limit(content, available_tokens)
        
    def estimate_request_cost(
        self, 
        system_prompt: str, 
        user_prompt: str, 
        completion_tokens: int = 800
    ) -> Dict[str, float]:
        """Estimate the cost of a request based on current OpenAI pricing."""
        # Pricing per 1M tokens (as of the knowledge cutoff)
        # Update these values as pricing changes
        pricing = {
            "gpt-4o-mini": {
                "input": 0.15,  # $0.15 per 1M input tokens
                "output": 0.60,  # $0.60 per 1M output tokens
            },
            # Add other models as needed
        }
        
        if self.model_name not in pricing:
            return {"error": f"Unknown model: {self.model_name}"}
            
        rates = pricing[self.model_name]
        
        input_tokens = self.count_tokens(system_prompt) + self.count_tokens(user_prompt)
        
        input_cost = (input_tokens / 1000000) * rates["input"]
        output_cost = (completion_tokens / 1000000) * rates["output"]
        total_cost = input_cost + output_cost
        
        return {
            "model": self.model_name,
            "input_tokens": input_tokens,
            "output_tokens": completion_tokens,
            "input_cost": input_cost,
            "output_cost": output_cost,
            "total_cost": total_cost
        }
```

Integrate token management with the LLM client:

```python
# Modify src/providers/llm_client.py
import json
from typing import Any, Dict, Optional
from openai import OpenAI
from ..config.settings import settings
from ..utils.token_manager import TokenManager

class LLMClient:
    def __init__(self):
        if not settings.OPENAI_API_KEY:
            raise RuntimeError("OPENAI_API_KEY not set")
        self.client = OpenAI(api_key=settings.OPENAI_API_KEY)
        self.token_manager = TokenManager(settings.LLM_MODEL)
        self._total_tokens_used = 0
        self._request_count = 0
        
    def complete_json(
        self, 
        system_prompt: str, 
        user_prompt: str,
        track_usage: bool = True
    ) -> Dict[str, Any]:
        # Estimate costs for logging/tracking
        cost_estimate = self.token_manager.estimate_request_cost(
            system_prompt, 
            user_prompt,
            settings.LLM_MAX_OUTPUT_TOKENS
        )
        
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
        
        # Track token usage if enabled
        if track_usage and hasattr(r, 'usage'):
            self._total_tokens_used += r.usage.total_tokens
            self._request_count += 1
            
            # Update the cost estimate with actual usage
            cost_estimate["actual_input_tokens"] = r.usage.prompt_tokens
            cost_estimate["actual_output_tokens"] = r.usage.completion_tokens
            cost_estimate["actual_total_tokens"] = r.usage.total_tokens
            
            # Recalculate costs with actual usage
            rates = {
                "input": 0.15 / 1000000,  # $0.15 per 1M input tokens
                "output": 0.60 / 1000000,  # $0.60 per 1M output tokens
            }
            cost_estimate["actual_input_cost"] = r.usage.prompt_tokens * rates["input"]
            cost_estimate["actual_output_cost"] = r.usage.completion_tokens * rates["output"]
            cost_estimate["actual_total_cost"] = cost_estimate["actual_input_cost"] + cost_estimate["actual_output_cost"]
        
        content = r.choices[0].message.content
        return json.loads(content)
        
    def get_usage_statistics(self) -> Dict[str, Any]:
        """Get token usage statistics."""
        return {
            "total_tokens_used": self._total_tokens_used,
            "request_count": self._request_count,
            "average_tokens_per_request": self._total_tokens_used / self._request_count if self._request_count > 0 else 0,
            "estimated_cost_usd": (self._total_tokens_used / 1000000) * 0.30  # Rough estimate at $0.30 per 1M tokens average
        }
```

### 3. Implement Content-Aware Prompt Optimization
**Priority: MEDIUM**

Create a system that adapts prompts based on content characteristics:

```python
# Create src/prompts/content_analyzer.py
import re
from typing import Dict, List, Tuple, Set, Optional
from bs4 import BeautifulSoup

class ContentAnalyzer:
    """Analyzes content to determine characteristics for prompt optimization."""
    
    def __init__(self):
        # Technical content markers
        self.tech_keywords = {
            "programming", "code", "function", "class", "algorithm",
            "framework", "library", "api", "server", "frontend", "backend",
            "javascript", "python", "java", "html", "css", "react", "node"
        }
        
        # News content markers
        self.news_keywords = {
            "news", "article", "report", "journalist", "correspondent",
            "breaking", "headline", "reported", "according", "statement",
            "press", "release", "announced", "today", "yesterday"
        }
        
        # Academic content markers
        self.academic_keywords = {
            "research", "study", "journal", "publication", "published",
            "academic", "university", "professor", "scientific", "hypothesis",
            "methodology", "conclusion", "findings", "experiment", "theory"
        }
        
    def analyze_content(self, text: str, title: Optional[str] = None, 
                        headings: Optional[List[str]] = None) -> Dict[str, Any]:
        """Analyze content characteristics to determine optimal prompt strategy."""
        content = (title or "") + " " + (text or "")
        headings_text = " ".join(headings or [])
        
        # Convert to lowercase for matching
        content_lower = content.lower()
        headings_lower = headings_text.lower()
        
        # Extract characteristics
        result = {
            "content_type": self._determine_content_type(content_lower, headings_lower),
            "reading_level": self._estimate_reading_level(content),
            "has_code_blocks": self._contains_code(content),
            "formality_level": self._assess_formality(content_lower),
            "content_density": self._assess_information_density(content),
            "estimated_word_count": len(content.split()),
            "language": self._detect_language(content)
        }
        
        # Extract key phrases for context
        result["key_phrases"] = self._extract_key_phrases(content, headings_text)
        
        # Content structure analysis
        result["structure"] = self._analyze_structure(content, headings or [])
        
        return result
        
    def _determine_content_type(self, content: str, headings: str) -> str:
        """Determine the likely content type."""
        # Check for technical content
        tech_score = sum(1 for word in self.tech_keywords if word in content)
        tech_score += sum(2 for word in self.tech_keywords if word in headings)
        
        # Check for news content
        news_score = sum(1 for word in self.news_keywords if word in content)
        news_score += sum(2 for word in self.news_keywords if word in headings)
        
        # Check for academic content
        academic_score = sum(1 for word in self.academic_keywords if word in content)
        academic_score += sum(2 for word in self.academic_keywords if word in headings)
        
        # Determine highest score
        max_score = max(tech_score, news_score, academic_score)
        
        if max_score < 3:
            return "general"
        elif tech_score == max_score:
            return "technical"
        elif news_score == max_score:
            return "news"
        elif academic_score == max_score:
            return "academic"
        else:
            return "general"
            
    def _estimate_reading_level(self, text: str) -> str:
        """Estimate reading level based on sentence and word complexity."""
        # Simple implementation - could be replaced with more sophisticated algorithms
        words = text.split()
        if not words:
            return "unknown"
            
        # Count sentences (roughly)
        sentences = re.split(r'[.!?]+', text)
        sentences = [s.strip() for s in sentences if s.strip()]
        
        if not sentences:
            return "unknown"
            
        # Calculate average words per sentence
        avg_words_per_sentence = len(words) / len(sentences)
        
        # Count complex words (simple approximation: words with 3+ syllables)
        complex_words = [w for w in words if self._count_syllables(w) >= 3]
        complex_word_percentage = len(complex_words) / len(words)
        
        # Simple heuristic
        if avg_words_per_sentence > 25 and complex_word_percentage > 0.2:
            return "advanced"
        elif avg_words_per_sentence > 15 and complex_word_percentage > 0.1:
            return "intermediate"
        else:
            return "basic"
            
    def _count_syllables(self, word: str) -> int:
        """Rough syllable counter."""
        # Very simplistic approximation
        word = word.lower()
        if len(word) <= 3:
            return 1
        count = 0
        vowels = "aeiouy"
        prev_is_vowel = False
        
        for char in word:
            is_vowel = char in vowels
            if is_vowel and not prev_is_vowel:
                count += 1
            prev_is_vowel = is_vowel
            
        # Adjust for common patterns
        if word.endswith('e'):
            count -= 1
        if word.endswith('le') and len(word) > 2 and word[-3] not in vowels:
            count += 1
            
        return max(1, count)
        
    def _contains_code(self, content: str) -> bool:
        """Check if content likely contains code blocks."""
        code_markers = [
            "```", "def ", "function(", "class ", "import ", "from ",
            "const ", "let ", "var ", "if (", "for (", "while (",
            "<div>", "<span>", "<script", "<html", "<?php"
        ]
        
        return any(marker in content for marker in code_markers)
        
    # Additional content analysis methods...
    # ...
```

Integrate content-aware prompt selection:

```python
# Modify src/services/analysis_service.py
from src.prompts.content_analyzer import ContentAnalyzer

class AnalysisService:
    def __init__(self):
        self.llm_client = LLMClient()
        self.content_analyzer = ContentAnalyzer()
        
    async def analyze_content(self, content: ScrapedContent) -> AnalysisReport:
        # Analyze content characteristics
        content_analysis = self.content_analyzer.analyze_content(
            text=content.main_text,
            title=content.title,
            headings=content.headings
        )
        
        # Select template based on content characteristics
        template = self._select_template_by_characteristics(content_analysis)
        
        # Prepare context for template
        context = {
            "url": content.url,
            "title": content.title or "",
            "meta": content.meta,
            "headings": content.headings,
            "content": content.main_text,
        }
        
        # Format prompts using template
        prompts = template.format(context)
        
        # Get analysis from LLM
        analysis_data = self.llm_client.complete_json(
            prompts["system_prompt"], 
            prompts["user_prompt"]
        )
        
        # Convert to AnalysisReport
        return AnalysisReport(**analysis_data)
        
    def _select_template_by_characteristics(self, content_analysis: Dict[str, Any]) -> PromptTemplate:
        """Select template based on content characteristics."""
        content_type = content_analysis["content_type"]
        
        if content_type == "technical":
            return TECHNICAL_CONTENT_TEMPLATE
        elif content_type == "news":
            return NEWS_CONTENT_TEMPLATE
        elif content_type == "academic":
            return ACADEMIC_CONTENT_TEMPLATE
        else:
            return CONTENT_ANALYSIS_TEMPLATE
```

### 4. Implement Few-Shot Learning in Templates
**Priority: MEDIUM**

Add examples to templates to improve output quality through few-shot learning:

```python
# Update src/prompts/templates.py with few-shot examples

CONTENT_ANALYSIS_TEMPLATE = PromptTemplate(
    template_id="content_analysis",
    version="1.1",
    system_template="""You are an expert content analyzer specializing in extracting key insights from web content.
Your analysis should be objective, thorough, and well-structured.

Your task is to analyze the provided web content and extract the following elements:
1. A concise summary of the main content (3-5 sentences)
2. Key points representing the main ideas (3-8 bullet points)
3. Main topics covered in the content (1-5 topics)
4. Important keywords relevant to the content
5. Overall sentiment (positive, neutral, or negative) with supporting evidence
6. Named entities mentioned (people, organizations, locations, etc.)
7. Estimate word count and reading grade level

Format your response as a JSON object matching the specified structure.

Here are two examples of excellent analyses:

EXAMPLE 1:
Input: 
TITLE: Climate Change Impact on Global Agriculture
CONTENT: Recent studies show that climate change is having a significant impact on global agriculture. Rising temperatures and changing precipitation patterns are affecting crop yields worldwide. Farmers in developing countries are particularly vulnerable due to limited resources for adaptation. Some regions are experiencing longer growing seasons, while others face increased drought and flooding. Researchers suggest that adaptive farming techniques and policy changes are necessary to ensure food security in the coming decades.

Output:
```json
{
  "summary": "Climate change is significantly affecting global agriculture through rising temperatures and changing precipitation patterns, impacting crop yields worldwide. Developing countries are especially vulnerable due to limited adaptation resources. While some regions benefit from longer growing seasons, others face increased drought and flooding, prompting researchers to recommend adaptive farming techniques and policy changes to ensure future food security.",
  "key_points": [
    "Climate change impacts global agriculture through temperature and precipitation changes",
    "Crop yields worldwide are being affected",
    "Developing countries are particularly vulnerable due to limited adaptation resources",
    "Effects vary by region - some see longer growing seasons, others more drought and flooding",
    "Adaptive farming techniques and policy changes are needed for food security"
  ],
  "topics": ["Climate Change", "Agriculture", "Food Security", "Environmental Impact", "Adaptation Strategies"],
  "keywords": ["climate change", "agriculture", "crop yields", "adaptation", "food security", "developing countries", "precipitation", "drought", "flooding", "farming techniques"],
  "sentiment": {
    "label": "neutral",
    "score": 0.0,
    "rationale": "The content presents factual information about climate change impacts without strong positive or negative language. It objectively describes challenges and potential solutions.",
    "evidence": [
      "Recent studies show that climate change is having a significant impact on global agriculture.",
      "Researchers suggest that adaptive farming techniques and policy changes are necessary to ensure food security in the coming decades."
    ]
  },
  "entities": [
    {"text": "developing countries", "type": "LOCATION"},
    {"text": "researchers", "type": "PERSON"}
  ],
  "word_count": 89,
  "reading_grade": "Grade 12 (College level)"
}
```

EXAMPLE 2:
[Additional example...]
""",
    user_template="""Analyze the following web content from {{ url }}:

{% if title %}TITLE: {{ title }}{% endif %}

{% if meta and meta.description %}
DESCRIPTION: {{ meta.description }}
{% endif %}

{% if headings %}
HEADINGS:
{% for heading in headings[:5] %}
- {{ heading }}
{% endfor %}
{% endif %}

CONTENT:
{{ content }}

Analyze this content and provide a structured analysis including summary, key points, topics, keywords, sentiment analysis, and entity recognition.
""",
    max_tokens=6000
)
```

### 5. Implement Template Testing and Performance Tracking
**Priority: LOW**

Create a system for tracking prompt performance and testing improvements:

```python
# Create src/prompts/template_testing.py
import json
import time
from typing import Dict, List, Any, Optional
from datetime import datetime
from .template_manager import PromptTemplate
from ..providers.llm_client import LLMClient

class TemplateTestResult:
    """Stores results from a template test."""
    def __init__(
        self,
        template_id: str,
        template_version: str,
        test_id: str,
        prompt_tokens: int,
        completion_tokens: int,
        latency_ms: float,
        estimated_cost: float,
        success: bool,
        error: Optional[str] = None
    ):
        self.template_id = template_id
        self.template_version = template_version
        self.test_id = test_id
        self.prompt_tokens = prompt_tokens
        self.completion_tokens = completion_tokens
        self.latency_ms = latency_ms
        self.estimated_cost = estimated_cost
        self.success = success
        self.error = error
        self.timestamp = datetime.now().isoformat()
        
    def to_dict(self) -> Dict[str, Any]:
        """Convert to dictionary for storage."""
        return {
            "template_id": self.template_id,
            "template_version": self.template_version,
            "test_id": self.test_id,
            "prompt_tokens": self.prompt_tokens,
            "completion_tokens": self.completion_tokens,
            "total_tokens": self.prompt_tokens + self.completion_tokens,
            "latency_ms": self.latency_ms,
            "estimated_cost": self.estimated_cost,
            "success": self.success,
            "error": self.error,
            "timestamp": self.timestamp
        }

class PromptTester:
    """Tests and evaluates prompt templates."""
    
    def __init__(self, llm_client: LLMClient):
        self.llm_client = llm_client
        self.results: List[TemplateTestResult] = []
        
    async def test_template(
        self,
        template: PromptTemplate,
        context: Dict[str, Any],
        test_id: str = None
    ) -> TemplateTestResult:
        """Test a template with a given context."""
        if test_id is None:
            test_id = f"test_{int(time.time())}"
            
        # Format prompts
        try:
            prompts = template.format(context)
            
            # Record start time
            start_time = time.time()
            
            # Get token counts
            prompt_tokens = template.get_token_count(
                prompts["system_prompt"], 
                prompts["user_prompt"]
            )
            
            # Call LLM
            try:
                result = self.llm_client.complete_json(
                    prompts["system_prompt"],
                    prompts["user_prompt"]
                )
                
                # Calculate latency
                latency_ms = (time.time() - start_time) * 1000
                
                # Get completion tokens (estimate from result)
                result_str = json.dumps(result)
                completion_tokens = len(result_str) // 4  # Rough estimate
                
                # Estimate cost
                cost = self._estimate_cost(prompt_tokens, completion_tokens)
                
                # Create test result
                test_result = TemplateTestResult(
                    template_id=template.template_id,
                    template_version=template.version,
                    test_id=test_id,
                    prompt_tokens=prompt_tokens,
                    completion_tokens=completion_tokens,
                    latency_ms=latency_ms,
                    estimated_cost=cost,
                    success=True
                )
                
            except Exception as e:
                # Calculate latency even for errors
                latency_ms = (time.time() - start_time) * 1000
                
                # Create error result
                test_result = TemplateTestResult(
                    template_id=template.template_id,
                    template_version=template.version,
                    test_id=test_id,
                    prompt_tokens=prompt_tokens,
                    completion_tokens=0,
                    latency_ms=latency_ms,
                    estimated_cost=self._estimate_cost(prompt_tokens, 0),
                    success=False,
                    error=str(e)
                )
                
        except Exception as e:
            # Template formatting error
            test_result = TemplateTestResult(
                template_id=template.template_id,
                template_version=template.version,
                test_id=test_id,
                prompt_tokens=0,
                completion_tokens=0,
                latency_ms=0,
                estimated_cost=0,
                success=False,
                error=f"Template formatting error: {str(e)}"
            )
            
        # Store result
        self.results.append(test_result)
        return test_result
        
    def _estimate_cost(self, prompt_tokens: int, completion_tokens: int) -> float:
        """Estimate the cost of the API call."""
        # Current pricing (update as needed)
        prompt_cost_per_token = 0.15 / 1000000  # $0.15 per million tokens
        completion_cost_per_token = 0.60 / 1000000  # $0.60 per million tokens
        
        prompt_cost = prompt_tokens * prompt_cost_per_token
        completion_cost = completion_tokens * completion_cost_per_token
        
        return prompt_cost + completion_cost
        
    def compare_templates(
        self,
        templates: List[PromptTemplate],
        contexts: List[Dict[str, Any]],
        test_id_prefix: str = "compare"
    ) -> Dict[str, Any]:
        """Compare multiple templates across multiple contexts."""
        all_results = []
        
        for i, context in enumerate(contexts):
            context_results = []
            context_id = f"{test_id_prefix}_context_{i}"
            
            for template in templates:
                result = self.test_template(
                    template=template,
                    context=context,
                    test_id=f"{context_id}_{template.template_id}_{template.version}"
                )
                context_results.append(result)
                
            all_results.append(context_results)
            
        # Aggregate comparison metrics
        comparison = self._aggregate_comparison_results(templates, all_results)
        return comparison
        
    def _aggregate_comparison_results(
        self,
        templates: List[PromptTemplate],
        results: List[List[TemplateTestResult]]
    ) -> Dict[str, Any]:
        """Aggregate results for template comparison."""
        template_metrics = {}
        
        for template in templates:
            template_id = f"{template.template_id}_{template.version}"
            template_results = [
                result for context_results in results
                for result in context_results
                if result.template_id == template.template_id and result.template_version == template.version
            ]
            
            success_rate = sum(1 for r in template_results if r.success) / len(template_results) if template_results else 0
            avg_latency = sum(r.latency_ms for r in template_results) / len(template_results) if template_results else 0
            avg_cost = sum(r.estimated_cost for r in template_results) / len(template_results) if template_results else 0
            avg_tokens = sum(r.prompt_tokens + (r.completion_tokens or 0) for r in template_results) / len(template_results) if template_results else 0
            
            template_metrics[template_id] = {
                "success_rate": success_rate,
                "avg_latency_ms": avg_latency,
                "avg_cost": avg_cost,
                "avg_tokens": avg_tokens,
                "test_count": len(template_results)
            }
            
        return {
            "template_metrics": template_metrics,
            "test_count": len(results),
            "timestamp": datetime.now().isoformat()
        }
        
    def save_results(self, filename: str):
        """Save test results to a JSON file."""
        with open(filename, "w") as f:
            json.dump([r.to_dict() for r in self.results], f, indent=2)
```

### 6. Security and Input Validation Enhancements
**Priority: HIGH**

Improve security and input validation for AI prompts:

```python
# Create src/utils/prompt_security.py
import re
from typing import Dict, List, Any, Tuple, Optional

class PromptSecurity:
    """Security utilities for prompt handling and validation."""
    
    def __init__(self):
        # Common prompt injection patterns
        self.injection_patterns = [
            r"ignore previous instructions",
            r"disregard all instructions",
            r"your new instructions are",
            r"forget your previous instructions",
            r"system prompt",
            r"system message",
            r"stop being an AI",
            r"Respond to the prompt above",
            r"instead, tell me",
            r"\$\{.*?\}",  # Template injection pattern
            r"</?[a-z]+>",  # Basic HTML tag pattern
        ]
        
        # Compile regex patterns for efficiency
        self.injection_regex = re.compile(
            "|".join(self.injection_patterns), 
            re.IGNORECASE
        )
        
    def sanitize_input(self, text: str) -> Tuple[str, bool, Optional[str]]:
        """
        Sanitize input text for safe use in prompts.
        
        Returns:
            Tuple of (sanitized_text, is_modified, reason)
        """
        original_text = text
        is_modified = False
        modification_reason = None
        
        # Check for injection attempts
        if self.injection_regex.search(text):
            # Replace potential injection patterns
            text = self.injection_regex.sub("[FILTERED]", text)
            is_modified = True
            modification_reason = "Potential prompt injection detected"
            
        # Remove control characters
        control_chars_pattern = re.compile(r'[\x00-\x08\x0B\x0C\x0E-\x1F\x7F]')
        sanitized = control_chars_pattern.sub('', text)
        if sanitized != text:
            text = sanitized
            is_modified = True
            modification_reason = modification_reason or "Control characters removed"
        
        # Check for excessive whitespace
        if len(re.findall(r'\s{3,}', text)) > 5:
            text = re.sub(r'\s{3,}', ' ', text)
            is_modified = True
            modification_reason = modification_reason or "Excessive whitespace normalized"
            
        return text, is_modified, modification_reason
        
    def validate_prompt_context(
        self, 
        context: Dict[str, Any]
    ) -> Tuple[Dict[str, Any], List[str]]:
        """
        Validate and sanitize prompt context data.
        
        Returns:
            Tuple of (sanitized_context, warnings)
        """
        sanitized_context = {}
        warnings = []
        
        for key, value in context.items():
            if isinstance(value, str):
                sanitized, modified, reason = self.sanitize_input(value)
                sanitized_context[key] = sanitized
                
                if modified:
                    warnings.append(f"Field '{key}' was modified: {reason}")
                    
            elif isinstance(value, (list, tuple)) and all(isinstance(item, str) for item in value):
                sanitized_list = []
                for i, item in enumerate(value):
                    sanitized_item, modified, reason = self.sanitize_input(item)
                    sanitized_list.append(sanitized_item)
                    
                    if modified:
                        warnings.append(f"Item {i} in field '{key}' was modified: {reason}")
                        
                sanitized_context[key] = sanitized_list
                
            elif isinstance(value, dict):
                # Recursively handle nested dictionaries
                sanitized_dict, nested_warnings = self.validate_prompt_context(value)
                sanitized_context[key] = sanitized_dict
                
                for warning in nested_warnings:
                    warnings.append(f"In nested field '{key}': {warning}")
                    
            else:
                # Non-string values (numbers, booleans, None) pass through unchanged
                sanitized_context[key] = value
                
        return sanitized_context, warnings
```

Integrate security measures into the template system:

```python
# Update src/prompts/template_manager.py
from ..utils.prompt_security import PromptSecurity

class PromptTemplate:
    def __init__(
        # ...existing code...
    ):
        # ...existing code...
        self.prompt_security = PromptSecurity()
        
    def format(self, context: Dict[str, Any]) -> Dict[str, str]:
        """Format the templates with the given context."""
        # Validate and sanitize context
        sanitized_context, warnings = self.prompt_security.validate_prompt_context(context)
        
        if warnings:
            # Log warnings but continue with sanitized context
            for warning in warnings:
                print(f"Prompt context warning: {warning}")
                
        # Continue with sanitized context
        system_prompt = self._system_template.render(**sanitized_context)
        user_prompt = self._user_template.render(**sanitized_context)
        
        # ...existing token counting and truncation code...
```

## Prompt Engineering Improvement Summary

The Web Content Analyzer has a functional but basic approach to prompt engineering. To improve its effectiveness and efficiency:

1. **Implement a formal template system** to standardize prompts, enable versioning, and facilitate optimization
2. **Add accurate token counting and budget management** using tiktoken to optimize costs and prevent token limit issues
3. **Develop content-aware prompt optimization** to select appropriate templates based on content characteristics
4. **Enhance prompts with few-shot learning examples** to improve output quality and consistency
5. **Create template testing and performance tracking** to measure and improve prompt effectiveness
6. **Implement security measures** to prevent prompt injection and ensure safe AI interactions

These improvements will create a more sophisticated prompt engineering system that produces better analysis results, optimizes token usage, and ensures secure AI interactions. The modular template system will make future enhancements and testing easier to implement.