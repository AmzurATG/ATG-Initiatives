# WebContentAnalyzer - Prompt Engineering & AI Input Design Review

## Executive Summary

The WebContentAnalyzer application utilizes a basic but functional approach to prompt engineering, with hardcoded templates and simple string substitution for content insertion. While the prompts are effective for basic content analysis, the prompt engineering implementation lacks sophistication in terms of dynamic generation, context management, and optimization. By enhancing the prompt design system with structured templates, dynamic generation capabilities, and proper prompt management, the application could achieve significantly better AI analysis results and flexibility.

**Prompt Engineering Quality Score: 5/10 (ADEQUATE)**

This score reflects a prompt engineering implementation that meets basic functional requirements but lacks advanced features, optimization, and management capabilities that would elevate the application to production quality.

## Prompt Design Quality & Effectiveness

### Current Prompt Design Implementation

The WebContentAnalyzer uses simple hardcoded string templates for prompt design:

```python
# Basic prompt templates defined as constants
ANALYSIS_PROMPT = """Analyze the following web content and provide a structured summary:
{content}

Please format your response as follows:
1. Main topic and brief overview
2. Key points
3. Tone analysis
4. Content quality assessment
"""

DETAILED_ANALYSIS_PROMPT = """Provide a detailed analysis of the following web content:
{content}

Include in your analysis:
1. Comprehensive summary (2-3 paragraphs)
2. Main topics and subtopics
3. Key insights and takeaways
4. Sentiment and tone analysis
5. Target audience assessment
6. Content quality and credibility evaluation
"""
```

### Prompt Design Strengths

1. **Clear instructions**: Prompts provide clear guidance on expected output
2. **Structured output format**: Request for specific formatting and sections
3. **Task-appropriate detail level**: Different templates for different analysis depths
4. **Content placeholder**: Proper content insertion with placeholder
5. **Appropriate tone and formality**: Professional and direct prompt style

### Prompt Design Weaknesses

1. **Limited specificity**: Generic instructions without domain-specific guidance
2. **Minimal examples**: No few-shot examples to guide output format
3. **Limited output constraints**: No detailed formatting requirements
4. **Hardcoded structure**: No dynamic adjustment to content type or length
5. **No specialized domain knowledge**: Generic analysis without topic specialization

### Prompt Design Evaluation

The current prompt designs are functional but basic. They could be significantly enhanced with:

- More specific instructions tailored to content types
- Few-shot examples for better output consistency
- Explicit constraints on output format and structure
- Domain-specific knowledge integration
- Structured JSON output specifications

## Dynamic Prompt Construction

### Current Implementation

The application uses simple string replacement for dynamic prompt construction:

```python
# Basic string replacement for content insertion
prompt = prompt_template.replace("{content}", content)
```

This implementation is minimal, with no dynamic prompt construction based on:
- Content type
- Analysis goals
- User preferences
- Available token budget
- Previous interaction history

### Dynamic Construction Strengths

1. **Simplicity**: Easy to understand and implement
2. **Reliability**: Minimal points of failure
3. **Predictability**: Consistent prompt structure

### Dynamic Construction Weaknesses

1. **Limited adaptability**: No content-specific prompt adjustment
2. **No context awareness**: Doesn't incorporate context or conversation history
3. **Wasteful token usage**: Doesn't optimize prompt based on token budget
4. **No personalization**: Doesn't adapt to user preferences or needs
5. **Limited meta-instruction**: Doesn't include system-level guidance based on content

### Sample Implementation of Enhanced Dynamic Prompt Construction

```python
def construct_dynamic_prompt(content, analysis_type, content_metadata=None, user_preferences=None, token_budget=4000):
    """Dynamically construct an optimized prompt based on content and context."""
    # Default base templates
    base_templates = {
        "default": PromptTemplate("Analyze the following web content: {content}\n\n{instructions}"),
        "detailed": PromptTemplate("Provide a comprehensive analysis of: {content}\n\n{detailed_instructions}"),
        "summary": PromptTemplate("Summarize the key points from: {content}\n\n{summary_instructions}")
    }
    
    # Get appropriate base template
    template = base_templates.get(analysis_type, base_templates["default"])
    
    # Determine content type and characteristics
    content_type = detect_content_type(content)
    content_length = len(content)
    estimated_tokens = estimate_tokens(content)
    
    # Generate appropriate instructions based on content type
    instructions = generate_instructions(content_type, content_length, analysis_type)
    
    # Add examples if appropriate for the content type
    if should_add_examples(content_type, estimated_tokens, token_budget):
        examples = get_relevant_examples(content_type, analysis_type)
        instructions += f"\n\nHere are examples of the expected format:\n{examples}"
    
    # Add output format specifications
    output_format = get_output_format(analysis_type, content_type, user_preferences)
    instructions += f"\n\nOutput format: {output_format}"
    
    # Handle token budget constraints
    if estimated_tokens > token_budget * 0.7:  # Leave 30% for response
        # Content is too large, implement reduction strategy
        content = reduce_content_for_token_budget(content, token_budget * 0.7)
        instructions += "\n\nNote: Content has been truncated for analysis."
    
    # Construct final prompt with all components
    prompt_params = {
        "content": content,
        "instructions": instructions,
        "detailed_instructions": instructions,  # Reuse for simplicity in this example
        "summary_instructions": instructions    # Reuse for simplicity in this example
    }
    
    return template.format(**prompt_params)
```

## Prompt Template Management

### Current Template Management

The WebContentAnalyzer uses a minimal approach to template management:

```python
# Templates defined as module-level constants
ANALYSIS_PROMPT = "..."
DETAILED_ANALYSIS_PROMPT = "..."
SUMMARY_PROMPT = "..."
```

No formal template management system exists for:
- Template versioning
- Template selection logic
- Template performance tracking
- A/B testing of templates
- Template customization or extension

### Template Management Strengths

1. **Simplicity**: Easy to maintain and understand
2. **Low overhead**: Minimal complexity in implementation
3. **Direct access**: Straightforward usage in code

### Template Management Weaknesses

1. **Limited flexibility**: Cannot easily modify templates at runtime
2. **No versioning**: No template history or versioning support
3. **Limited reusability**: No shared components between templates
4. **No performance tracking**: Cannot measure template effectiveness
5. **Hard to maintain**: Changes require code modifications

### Proposed Template Management Solution

```python
from dataclasses import dataclass
from typing import Dict, List, Optional, Any
import json
import os

@dataclass
class PromptTemplate:
    """A structured prompt template with metadata and formatting capabilities."""
    name: str
    version: str
    template: str
    parameters: List[str]
    description: Optional[str] = None
    examples: Optional[List[Dict[str, Any]]] = None
    metadata: Optional[Dict[str, Any]] = None
    
    def format(self, **kwargs) -> str:
        """Format the template with the provided parameters."""
        # Validate required parameters
        missing_params = [p for p in self.parameters if p not in kwargs]
        if missing_params:
            raise ValueError(f"Missing required parameters: {', '.join(missing_params)}")
        
        # Format the template
        formatted = self.template
        for key, value in kwargs.items():
            placeholder = "{" + key + "}"
            formatted = formatted.replace(placeholder, str(value))
            
        return formatted
    
    def to_dict(self) -> Dict[str, Any]:
        """Convert template to dictionary for serialization."""
        return {
            "name": self.name,
            "version": self.version,
            "template": self.template,
            "parameters": self.parameters,
            "description": self.description,
            "examples": self.examples,
            "metadata": self.metadata
        }
    
    @classmethod
    def from_dict(cls, data: Dict[str, Any]) -> "PromptTemplate":
        """Create template from dictionary."""
        return cls(
            name=data["name"],
            version=data["version"],
            template=data["template"],
            parameters=data["parameters"],
            description=data.get("description"),
            examples=data.get("examples"),
            metadata=data.get("metadata")
        )
        
    @classmethod
    def from_json(cls, json_str: str) -> "PromptTemplate":
        """Create template from JSON string."""
        return cls.from_dict(json.loads(json_str))


class PromptTemplateManager:
    """Manages a collection of prompt templates with versioning."""
    
    def __init__(self, template_dir: Optional[str] = None):
        self.templates: Dict[str, Dict[str, PromptTemplate]] = {}
        self.template_dir = template_dir
        
        # Load templates from directory if provided
        if template_dir and os.path.isdir(template_dir):
            self.load_templates_from_directory(template_dir)
    
    def add_template(self, template: PromptTemplate) -> None:
        """Add a template to the manager."""
        if template.name not in self.templates:
            self.templates[template.name] = {}
        
        self.templates[template.name][template.version] = template
    
    def get_template(self, name: str, version: Optional[str] = None) -> PromptTemplate:
        """Get a template by name and optionally version."""
        if name not in self.templates:
            raise ValueError(f"Template '{name}' not found")
        
        versions = self.templates[name]
        
        # Return specific version if requested
        if version:
            if version not in versions:
                raise ValueError(f"Version '{version}' of template '{name}' not found")
            return versions[version]
        
        # Otherwise return latest version
        latest_version = max(versions.keys())
        return versions[latest_version]
    
    def format_template(self, name: str, version: Optional[str] = None, **kwargs) -> str:
        """Format a template with provided parameters."""
        template = self.get_template(name, version)
        return template.format(**kwargs)
    
    def save_template(self, template: PromptTemplate) -> None:
        """Save a template to the template directory."""
        if not self.template_dir:
            raise ValueError("Template directory not set")
        
        os.makedirs(self.template_dir, exist_ok=True)
        
        filename = f"{template.name}_v{template.version}.json"
        filepath = os.path.join(self.template_dir, filename)
        
        with open(filepath, "w") as f:
            json.dump(template.to_dict(), f, indent=2)
    
    def load_templates_from_directory(self, directory: str) -> None:
        """Load all template files from a directory."""
        for filename in os.listdir(directory):
            if filename.endswith(".json"):
                filepath = os.path.join(directory, filename)
                try:
                    with open(filepath, "r") as f:
                        template_data = json.load(f)
                        template = PromptTemplate.from_dict(template_data)
                        self.add_template(template)
                except Exception as e:
                    print(f"Error loading template '{filename}': {e}")


# Example usage
template_manager = PromptTemplateManager("./prompt_templates")

# Define templates
default_analysis = PromptTemplate(
    name="content_analysis",
    version="1.0",
    template="Analyze the following web content:\n\n{content}\n\nProvide analysis with these sections:\n{sections}",
    parameters=["content", "sections"],
    description="Basic web content analysis template"
)

detailed_analysis = PromptTemplate(
    name="content_analysis",
    version="1.1",
    template="Provide a detailed analysis of this web content:\n\n{content}\n\n{instructions}\n\nReturn as JSON with fields: {fields}",
    parameters=["content", "instructions", "fields"],
    description="Detailed analysis with JSON output format"
)

# Add templates to manager
template_manager.add_template(default_analysis)
template_manager.add_template(detailed_analysis)

# Use the template
analysis_prompt = template_manager.format_template(
    name="content_analysis", 
    # version="1.0", # Optional - omit for latest version
    content="...",
    sections="1. Summary\n2. Key points\n3. Sentiment"
)
```

## Context Management & Conversation Flow

### Current Context Management

The WebContentAnalyzer has minimal context management, with each analysis operating independently:

```python
# No conversation or context management
async def analyze_content(content, analysis_type="default"):
    if analysis_type == "default":
        result = await analyze_with_llm(content, DEFAULT_ANALYSIS_PROMPT)
    elif analysis_type == "detailed":
        result = await analyze_with_llm(content, DETAILED_ANALYSIS_PROMPT)
    return result
```

### Context Management Weaknesses

1. **No conversation history**: Each query is standalone without context
2. **No document context**: Analysis doesn't consider document structure
3. **No user context**: No personalization based on user interaction
4. **No contextual refinement**: Cannot refine analysis based on previous results
5. **No multi-stage processing**: No sequential analysis or follow-up

### Proposed Context Management Solution

```python
class ConversationContext:
    """Manages conversation state and context for AI interactions."""
    
    def __init__(self, user_id: str, session_id: str):
        self.user_id = user_id
        self.session_id = session_id
        self.conversation_history = []
        self.document_context = {}
        self.user_preferences = {}
        self.analysis_results = []
        self.created_at = datetime.now().isoformat()
        self.last_updated = self.created_at
        self.max_history_length = 10
    
    def add_message(self, role: str, content: str, metadata: Optional[Dict[str, Any]] = None) -> None:
        """Add a message to the conversation history."""
        self.conversation_history.append({
            "role": role,
            "content": content,
            "timestamp": datetime.now().isoformat(),
            "metadata": metadata or {}
        })
        
        # Trim history if it gets too long
        if len(self.conversation_history) > self.max_history_length:
            # Keep the first system message and the most recent messages
            system_messages = [m for m in self.conversation_history if m["role"] == "system"]
            if system_messages:
                first_system = system_messages[0]
                recent_messages = self.conversation_history[-self.max_history_length + 1:]
                self.conversation_history = [first_system] + recent_messages
            else:
                self.conversation_history = self.conversation_history[-self.max_history_length:]
        
        self.last_updated = datetime.now().isoformat()
    
    def set_document_context(self, document_data: Dict[str, Any]) -> None:
        """Set or update document context."""
        self.document_context = document_data
        self.last_updated = datetime.now().isoformat()
    
    def update_user_preferences(self, preferences: Dict[str, Any]) -> None:
        """Update user preferences."""
        self.user_preferences.update(preferences)
        self.last_updated = datetime.now().isoformat()
    
    def add_analysis_result(self, result: Dict[str, Any]) -> None:
        """Add an analysis result to context."""
        self.analysis_results.append({
            "result": result,
            "timestamp": datetime.now().isoformat()
        })
        self.last_updated = datetime.now().isoformat()
    
    def get_context_for_prompt(self, include_history: bool = True, 
                             include_document: bool = True,
                             include_preferences: bool = True,
                             include_results: bool = True) -> Dict[str, Any]:
        """Get the context data for inclusion in a prompt."""
        context = {}
        
        if include_history and self.conversation_history:
            context["conversation_history"] = self.conversation_history
        
        if include_document and self.document_context:
            context["document_context"] = self.document_context
            
        if include_preferences and self.user_preferences:
            context["user_preferences"] = self.user_preferences
            
        if include_results and self.analysis_results:
            context["previous_analyses"] = self.analysis_results
            
        return context
    
    def to_dict(self) -> Dict[str, Any]:
        """Convert context to dictionary for serialization."""
        return {
            "user_id": self.user_id,
            "session_id": self.session_id,
            "conversation_history": self.conversation_history,
            "document_context": self.document_context,
            "user_preferences": self.user_preferences,
            "analysis_results": self.analysis_results,
            "created_at": self.created_at,
            "last_updated": self.last_updated
        }
    
    @classmethod
    def from_dict(cls, data: Dict[str, Any]) -> "ConversationContext":
        """Create context from dictionary."""
        context = cls(data["user_id"], data["session_id"])
        context.conversation_history = data.get("conversation_history", [])
        context.document_context = data.get("document_context", {})
        context.user_preferences = data.get("user_preferences", {})
        context.analysis_results = data.get("analysis_results", [])
        context.created_at = data.get("created_at", datetime.now().isoformat())
        context.last_updated = data.get("last_updated", context.created_at)
        return context


class ContextAwarePromptGenerator:
    """Generates context-aware prompts based on conversation context."""
    
    def __init__(self, template_manager: PromptTemplateManager):
        self.template_manager = template_manager
    
    def generate_prompt(self, context: ConversationContext, template_name: str, 
                       content: str, analysis_type: str) -> str:
        """Generate a context-aware prompt."""
        # Get context data for prompt
        ctx_data = context.get_context_for_prompt()
        
        # Determine relevant historical context
        previous_analyses = ctx_data.get("previous_analyses", [])
        relevant_history = self._get_relevant_history(previous_analyses, content)
        
        # Build instructions based on context and content
        instructions = self._build_instructions(analysis_type, ctx_data, relevant_history)
        
        # Determine output format based on user preferences
        user_prefs = ctx_data.get("user_preferences", {})
        output_format = user_prefs.get("output_format", "default")
        output_fields = self._get_output_fields(output_format, analysis_type)
        
        # Format the template with all context information
        return self.template_manager.format_template(
            name=template_name,
            content=content,
            instructions=instructions,
            fields=output_fields,
            context=json.dumps(ctx_data) if ctx_data else ""
        )
    
    def _get_relevant_history(self, previous_analyses, content):
        """Extract relevant history based on content similarity."""
        # Simplified implementation
        if not previous_analyses:
            return []
            
        # In a real implementation, this would use semantic similarity
        # to find relevant previous analyses
        return previous_analyses[-3:]  # Just the last 3 for this example
    
    def _build_instructions(self, analysis_type, ctx_data, relevant_history):
        """Build custom instructions based on context."""
        instructions = []
        
        if analysis_type == "detailed":
            instructions.append("Perform a comprehensive analysis with multiple perspectives.")
        else:
            instructions.append("Provide a concise but informative analysis.")
            
        if relevant_history:
            instructions.append("Consider these previous analyses when forming your response:")
            for idx, analysis in enumerate(relevant_history):
                summary = analysis["result"].get("summary", "")[:100] + "..."
                instructions.append(f"{idx+1}. Previous analysis: {summary}")
                
        if ctx_data.get("document_context"):
            doc_type = ctx_data["document_context"].get("type")
            if doc_type:
                instructions.append(f"This is a {doc_type} document. Analyze accordingly.")
                
        return "\n".join(instructions)
    
    def _get_output_fields(self, output_format, analysis_type):
        """Determine output fields based on format and analysis type."""
        base_fields = ["summary", "key_points", "sentiment"]
        
        if output_format == "json":
            if analysis_type == "detailed":
                return base_fields + ["topics", "audience", "credibility", "recommendations"]
            return base_fields + ["topics"]
            
        elif output_format == "markdown":
            return "markdown headings for: " + ", ".join(base_fields)
            
        return ", ".join(base_fields)


# Example usage
context = ConversationContext("user123", "session456")
context.set_document_context({"type": "blog_post", "source": "corporate_website"})
context.update_user_preferences({"output_format": "json", "detail_level": "high"})

template_manager = PromptTemplateManager()
prompt_generator = ContextAwarePromptGenerator(template_manager)

prompt = prompt_generator.generate_prompt(
    context=context,
    template_name="content_analysis",
    content="Article text goes here...",
    analysis_type="detailed"
)
```

## Token Optimization & Cost Management

### Current Token Usage Analysis

The WebContentAnalyzer has no explicit token optimization or management:

```python
# No token counting or optimization
async def analyze_with_llm(content, prompt_template):
    prompt = prompt_template.replace("{content}", content)
    # No content length check or token estimation
    response = await openai.ChatCompletion.acreate(
        model="gpt-3.5-turbo",
        messages=[
            {"role": "system", "content": "You are a helpful assistant."},
            {"role": "user", "content": prompt}
        ],
        temperature=0.7,
        max_tokens=1000
    )
```

### Token Usage Weaknesses

1. **No token counting**: No estimation of token usage before API calls
2. **No content truncation**: Long content sent without optimization
3. **No cost tracking**: No measurement of token usage and costs
4. **Fixed max_tokens**: No dynamic adjustment based on content
5. **No model selection**: No cost-based model choice

### Proposed Token Optimization Implementation

```python
import tiktoken
from dataclasses import dataclass
from typing import Dict, List, Optional, Any, Tuple

@dataclass
class TokenUsage:
    """Tracks token usage and costs."""
    prompt_tokens: int
    completion_tokens: int
    total_tokens: int
    model: str
    cost: float

    @classmethod
    def from_openai_response(cls, response, model: str) -> "TokenUsage":
        """Create from OpenAI API response."""
        usage = response.usage
        cost = cls.calculate_cost(
            usage.prompt_tokens, 
            usage.completion_tokens, 
            model
        )
        return cls(
            prompt_tokens=usage.prompt_tokens,
            completion_tokens=usage.completion_tokens,
            total_tokens=usage.total_tokens,
            model=model,
            cost=cost
        )
    
    @staticmethod
    def calculate_cost(prompt_tokens: int, completion_tokens: int, model: str) -> float:
        """Calculate cost based on token usage and model."""
        costs = {
            "gpt-3.5-turbo": {"prompt": 0.0015, "completion": 0.002},
            "gpt-4": {"prompt": 0.03, "completion": 0.06},
            "gpt-4-32k": {"prompt": 0.06, "completion": 0.12}
        }
        
        model_cost = costs.get(model, costs["gpt-3.5-turbo"])
        prompt_cost = (prompt_tokens / 1000) * model_cost["prompt"]
        completion_cost = (completion_tokens / 1000) * model_cost["completion"]
        
        return round(prompt_cost + completion_cost, 6)


class TokenManager:
    """Manages token usage, estimation, and optimization."""
    
    def __init__(self):
        self.encoders = {}
        self.usage_log = []
        self.total_cost = 0.0
        
    def get_encoder(self, model: str):
        """Get or create a token encoder for the specified model."""
        if model not in self.encoders:
            try:
                self.encoders[model] = tiktoken.encoding_for_model(model)
            except KeyError:
                # Fall back to cl100k_base for new models
                self.encoders[model] = tiktoken.get_encoding("cl100k_base")
        return self.encoders[model]
    
    def estimate_tokens(self, text: str, model: str = "gpt-3.5-turbo") -> int:
        """Estimate the number of tokens in a text."""
        encoder = self.get_encoder(model)
        return len(encoder.encode(text))
    
    def optimize_content(self, content: str, max_tokens: int, 
                        model: str = "gpt-3.5-turbo") -> str:
        """Optimize content to fit within token limit."""
        estimated_tokens = self.estimate_tokens(content, model)
        
        if estimated_tokens <= max_tokens:
            return content
        
        # Simple truncation approach - more sophisticated would be semantic chunking
        encoder = self.get_encoder(model)
        tokens = encoder.encode(content)
        truncated_tokens = tokens[:max_tokens]
        
        return encoder.decode(truncated_tokens)
    
    def optimize_messages(self, messages: List[Dict[str, str]], 
                         max_tokens: int, 
                         model: str = "gpt-3.5-turbo") -> Tuple[List[Dict[str, str]], int]:
        """Optimize message list to fit within token limit."""
        total_tokens = 0
        optimized_messages = []
        
        # Process messages in reverse order (newest first)
        # System message and latest user message are highest priority
        system_messages = [m for m in messages if m["role"] == "system"]
        user_messages = [m for m in messages if m["role"] == "user"]
        assistant_messages = [m for m in messages if m["role"] == "assistant"]
        
        # Estimate base token usage for message formatting
        format_tokens = 3  # Conservative estimate per message
        
        # First add system message(s) if any
        for msg in system_messages:
            msg_tokens = self.estimate_tokens(msg["content"], model) + format_tokens
            if total_tokens + msg_tokens <= max_tokens:
                optimized_messages.append(msg)
                total_tokens += msg_tokens
            else:
                # Truncate system message if needed
                truncated_content = self.optimize_content(
                    msg["content"], 
                    max_tokens - total_tokens - format_tokens,
                    model
                )
                optimized_messages.append({"role": msg["role"], "content": truncated_content})
                total_tokens += self.estimate_tokens(truncated_content, model) + format_tokens
        
        # Then add the most recent user message (highest priority)
        if user_messages:
            latest_user_msg = user_messages[-1]
            msg_tokens = self.estimate_tokens(latest_user_msg["content"], model) + format_tokens
            
            if total_tokens + msg_tokens <= max_tokens:
                optimized_messages.append(latest_user_msg)
                total_tokens += msg_tokens
            else:
                # Truncate latest user message if needed
                truncated_content = self.optimize_content(
                    latest_user_msg["content"], 
                    max_tokens - total_tokens - format_tokens,
                    model
                )
                optimized_messages.append({"role": "user", "content": truncated_content})
                total_tokens += self.estimate_tokens(truncated_content, model) + format_tokens
        
        # If we have room, add alternating assistant/user messages for context
        remaining_user = user_messages[:-1] if user_messages else []
        messages_pairs = list(zip(
            reversed(assistant_messages), 
            reversed(remaining_user)
        ))
        
        for assistant_msg, user_msg in messages_pairs:
            # Try to add the assistant message
            if assistant_msg:
                msg_tokens = self.estimate_tokens(assistant_msg["content"], model) + format_tokens
                if total_tokens + msg_tokens <= max_tokens:
                    optimized_messages.append(assistant_msg)
                    total_tokens += msg_tokens
                else:
                    # Not enough space, stop adding messages
                    break
            
            # Try to add the user message
            if user_msg:
                msg_tokens = self.estimate_tokens(user_msg["content"], model) + format_tokens
                if total_tokens + msg_tokens <= max_tokens:
                    optimized_messages.append(user_msg)
                    total_tokens += msg_tokens
                else:
                    # Not enough space, stop adding messages
                    break
        
        # Sort messages back into correct chronological order
        optimized_messages.sort(key=lambda m: (
            0 if m["role"] == "system" else 
            (1 if m in user_messages else 2)
        ))
        
        return optimized_messages, total_tokens
    
    def record_usage(self, token_usage: TokenUsage) -> None:
        """Record token usage and update totals."""
        self.usage_log.append({
            "timestamp": datetime.now().isoformat(),
            "model": token_usage.model,
            "prompt_tokens": token_usage.prompt_tokens,
            "completion_tokens": token_usage.completion_tokens,
            "total_tokens": token_usage.total_tokens,
            "cost": token_usage.cost
        })
        
        self.total_cost += token_usage.cost
    
    def get_usage_summary(self) -> Dict[str, Any]:
        """Get a summary of token usage and costs."""
        if not self.usage_log:
            return {"total_cost": 0, "total_tokens": 0, "calls": 0}
        
        model_usage = {}
        total_tokens = 0
        
        for entry in self.usage_log:
            model = entry["model"]
            if model not in model_usage:
                model_usage[model] = {
                    "calls": 0,
                    "tokens": 0,
                    "cost": 0
                }
                
            model_usage[model]["calls"] += 1
            model_usage[model]["tokens"] += entry["total_tokens"]
            model_usage[model]["cost"] += entry["cost"]
            total_tokens += entry["total_tokens"]
        
        return {
            "total_cost": self.total_cost,
            "total_tokens": total_tokens,
            "calls": len(self.usage_log),
            "by_model": model_usage
        }


# Example usage
token_manager = TokenManager()

def analyze_with_token_optimization(content, prompt_template, model="gpt-3.5-turbo", max_tokens=1000):
    # Build messages
    messages = [
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": prompt_template.replace("{content}", content)}
    ]
    
    # Calculate available token space
    context_limit = 4096  # For gpt-3.5-turbo
    if model.startswith("gpt-4"):
        context_limit = 8192  # Basic gpt-4
    if "32k" in model:
        context_limit = 32768  # gpt-4-32k
        
    # Reserve tokens for completion
    max_input_tokens = context_limit - max_tokens
    
    # Optimize messages to fit token limit
    optimized_messages, estimated_tokens = token_manager.optimize_messages(
        messages, max_input_tokens, model
    )
    
    print(f"Estimated tokens: {estimated_tokens} (limit: {max_input_tokens})")
    
    # Make the API call with optimized content
    response = await openai.ChatCompletion.acreate(
        model=model,
        messages=optimized_messages,
        temperature=0.7,
        max_tokens=max_tokens
    )
    
    # Record usage
    token_usage = TokenUsage.from_openai_response(response, model)
    token_manager.record_usage(token_usage)
    
    print(f"Actual tokens used: {token_usage.total_tokens} (cost: ${token_usage.cost:.6f})")
    
    return {
        "content": response.choices[0].message.content,
        "usage": token_usage
    }
```

## Safety & Security in Prompt Design

### Current Safety Implementation

The WebContentAnalyzer has minimal safety measures in its prompt design:

```python
# No prompt safety measures beyond basic system role
def build_llm_prompt(chunk: dict, document_outline: Optional[dict] = None, language: str = "en") -> str:
    disclaimer = (
        "Note: The following analysis is generated by an AI language model and may contain inaccuracies, "
        "hallucinations, or misinterpretations. Do not treat as factual or authoritative."
    )
    # Basic prompt construction
    prompt = (
        f"{disclaimer}\n"
        f"You are analyzing the following section of a web document.\n"
        # ...rest of prompt...
    )
```

### Safety Weaknesses

1. **Limited prompt injection protection**: No validation for injection attacks
2. **No sensitive data filtering**: No screening for PII in content
3. **No content moderation**: No safety checks for harmful content
4. **Limited output filtering**: No validation of generated content
5. **Basic system prompt**: No specialized safety instructions

### Proposed Safety Enhancement Implementation

```python
import re
from typing import Dict, List, Optional, Any, Tuple

class PromptSafetyManager:
    """Manages prompt safety, security, and content moderation."""
    
    def __init__(self):
        self.injection_patterns = [
            r"ignore (all |)previous instructions",
            r"ignore (all |)prior instructions",
            r"disregard (all |)previous instructions",
            r"disregard (all |)prior instructions",
            r"forget (all |)previous instructions",
            r"forget (all |)prior instructions",
            r"do not follow (the |)instructions",
            r"do not adhere to (the |)guidelines",
            r"bypass (the |)content filtering",
            r"bypass (the |)content moderation",
        ]
        
        self.pii_patterns = [
            # Social Security Numbers (US)
            r"\b\d{3}[-\s]?\d{2}[-\s]?\d{4}\b",
            # Credit Card Numbers
            r"\b(?:\d{4}[-\s]?){3}\d{4}\b",
            # Email addresses
            r"\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b",
            # Phone numbers
            r"\b\(?\d{3}\)?[-.\s]?\d{3}[-.\s]?\d{4}\b",
        ]
        
        self.sensitive_terms = [
            "password", "passcode", "secret", "private key", "api key", 
            "token", "auth", "credentials", "login", "oauth"
        ]
        
        self.moderation_terms = [
            "obscene", "explicit", "offensive", "hate speech", 
            "illegal activity", "harmful", "dangerous", "racist",
            "sexist", "homophobic", "transphobic", "discriminatory"
        ]
    
    def detect_prompt_injection(self, content: str) -> List[str]:
        """Detect potential prompt injection attacks."""
        detected = []
        content_lower = content.lower()
        
        for pattern in self.injection_patterns:
            matches = re.findall(pattern, content_lower, re.IGNORECASE)
            if matches:
                detected.append(f"Potential prompt injection: '{pattern}'")
                
        # Check for unusual sequences or commands
        if "```" in content and any(cmd in content_lower for cmd in ["system", "user", "assistant"]):
            detected.append("Potential role specification injection")
            
        return detected
    
    def detect_pii(self, content: str) -> List[str]:
        """Detect personally identifiable information."""
        detected = []
        
        for pattern in self.pii_patterns:
            matches = re.findall(pattern, content)
            if matches:
                detected.append(f"Potential PII detected: {pattern}")
                
        for term in self.sensitive_terms:
            if re.search(r'\b' + re.escape(term) + r'\b', content.lower()):
                detected.append(f"Sensitive term detected: '{term}'")
                
        return detected
    
    def check_content_moderation(self, content: str) -> List[str]:
        """Check content for moderation concerns."""
        # In production, this would call a content moderation API
        # For this example, using simple keyword matching
        concerns = []
        
        for term in self.moderation_terms:
            if re.search(r'\b' + re.escape(term) + r'\b', content.lower()):
                concerns.append(f"Moderation concern: '{term}'")
                
        return concerns
    
    def sanitize_content(self, content: str) -> Tuple[str, List[str]]:
        """Sanitize content for safe prompt creation."""
        sanitized = content
        modifications = []
        
        # Replace potential PII
        for pattern in self.pii_patterns:
            replaced, count = re.subn(pattern, "[REDACTED]", sanitized)
            if count > 0:
                sanitized = replaced
                modifications.append(f"Redacted {count} instances of PII matching {pattern}")
                
        # Sanitize injection attempts
        for pattern in self.injection_patterns:
            replaced, count = re.subn(pattern, "[FILTERED]", sanitized, flags=re.IGNORECASE)
            if count > 0:
                sanitized = replaced
                modifications.append(f"Filtered {count} potential prompt injection attempts")
        
        # Add safety markers
        if modifications:
            sanitized = "[SAFETY NOTICE: This content has been automatically sanitized]\n\n" + sanitized
            
        return sanitized, modifications
    
    def create_safe_system_prompt(self, base_prompt: str) -> str:
        """Create a safety-enhanced system prompt."""
        safety_instructions = [
            "Prioritize user safety and privacy in your responses.",
            "Do not generate harmful, illegal, unethical or deceptive content.",
            "Do not provide specific instructions for dangerous or illegal activities.",
            "If asked to analyze potentially harmful content, focus only on general concepts.",
            "Do not reveal or handle any personal identifiable information (PII).",
            "If unsure about the safety of a request, err on the side of caution."
        ]
        
        safety_section = "\n".join([f"- {instruction}" for instruction in safety_instructions])
        
        return f"{base_prompt}\n\nSAFETY GUIDELINES:\n{safety_section}"
    
    def validate_prompt(self, prompt: str, content: str) -> Dict[str, Any]:
        """Validate a prompt for security concerns."""
        validation_results = {
            "safe": True,
            "warnings": [],
            "critical_issues": [],
            "modifications": []
        }
        
        # Check content
        pii_detections = self.detect_pii(content)
        injection_detections = self.detect_prompt_injection(content)
        moderation_concerns = self.check_content_moderation(content)
        
        # Check prompt itself
        prompt_injection_detections = self.detect_prompt_injection(prompt)
        prompt_moderation_concerns = self.check_content_moderation(prompt)
        
        # Collect all issues
        validation_results["warnings"].extend(pii_detections)
        validation_results["warnings"].extend(moderation_concerns)
        
        validation_results["critical_issues"].extend(injection_detections)
        validation_results["critical_issues"].extend(prompt_injection_detections)
        validation_results["critical_issues"].extend(prompt_moderation_concerns)
        
        # Determine safety
        validation_results["safe"] = not (validation_results["critical_issues"])
        
        # Sanitize if needed
        if not validation_results["safe"] or pii_detections:
            sanitized_content, modifications = self.sanitize_content(content)
            validation_results["modifications"] = modifications
            validation_results["sanitized_content"] = sanitized_content
            
        return validation_results


# Example usage
safety_manager = PromptSafetyManager()

def prepare_safe_prompt(content, prompt_template):
    # Validate content first
    validation = safety_manager.validate_prompt(prompt_template, content)
    
    if not validation["safe"]:
        # Log security concerns
        for issue in validation["critical_issues"]:
            logging.warning(f"Prompt security issue: {issue}")
        
        # Use sanitized content if available
        if "sanitized_content" in validation:
            content = validation["sanitized_content"]
            logging.info(f"Content sanitized with modifications: {validation['modifications']}")
    
    # Enhance system prompt with safety instructions
    system_prompt = safety_manager.create_safe_system_prompt(
        "You are a helpful web content analyzer."
    )
    
    # Create final prompt with sanitized content
    final_prompt = prompt_template.replace("{content}", content)
    
    return {
        "messages": [
            {"role": "system", "content": system_prompt},
            {"role": "user", "content": final_prompt}
        ],
        "security_validation": validation
    }
```

## Prompt Engineering Quality Assessment

### Overall Prompt Engineering Strengths

1. **Functional Implementation**: The application has a working prompt system that produces useful results.
2. **Clear Instruction Structure**: Prompts include clear instructions with structured outputs.
3. **Domain-Specific Focus**: Prompts are tailored specifically for web content analysis.
4. **Error Handling**: Basic error handling for LLM service failures.
5. **Disclaimer Inclusion**: Includes AI-generated content disclaimer for user awareness.

### Overall Prompt Engineering Weaknesses

1. **Primitive Template System**: Simple string replacement with no structured template management.
2. **Limited Prompt Adaptability**: Fixed templates with no dynamic adjustment based on content.
3. **Inefficient Token Usage**: No token counting, optimization, or cost management.
4. **No Context Management**: Each prompt is standalone with no conversation context.
5. **Limited Safety Measures**: Basic disclaimer but no prompt injection protection.

## Prompt Engineering Recommendations & Action Plan

### Critical Improvements (Implement Immediately)

1. **Implement structured prompt template management**:
   - Create a `PromptTemplate` class with parameters and formatting
   - Set up template versioning and organization
   - Move templates from hardcoded strings to structured objects

2. **Add token counting and optimization**:
   - Implement token estimation before API calls
   - Optimize content to fit within token limits
   - Track token usage and costs

3. **Enhance prompt safety and security**:
   - Add prompt injection detection and prevention
   - Implement content moderation and PII detection
   - Create enhanced safety instructions in system prompts

### High-Priority Improvements (Implement Next Sprint)

1. **Implement context-aware prompting**:
   - Create conversation context management
   - Add document context integration in prompts
   - Develop multi-turn conversation capabilities

2. **Create dynamic prompt generation**:
   - Build content-specific prompt adjustment
   - Implement few-shot example selection logic
   - Add output format specification based on use case

3. **Implement content chunking and summarization**:
   - Add semantic chunking for large documents
   - Create hierarchical summarization system
   - Implement multi-step analysis for large content

### Medium-Priority Improvements (Next 1-2 Months)

1. **Develop prompt performance tracking**:
   - Implement prompt effectiveness measurement
   - Create A/B testing framework for templates
   - Build prompt optimization feedback loop

2. **Create advanced output parsing**:
   - Add structured output validation
   - Implement fallback strategies for malformed responses
   - Build response quality assessment system

3. **Implement domain-specific prompt enhancement**:
   - Create specialized templates for different content types
   - Add domain knowledge integration in prompts
   - Build taxonomy-aware prompt construction

## Prompt Engineering Learning Resources

1. **Recommended Books and Courses**:
   - "The Art of Prompt Engineering" by Various Authors
   - OpenAI's Prompt Engineering Guide
   - LangChain Documentation on Prompt Templates and Chains

2. **Useful Libraries for Prompt Management**:
   - LangChain for prompt management and chains
   - Guidance for structured prompting
   - tiktoken for token counting and optimization

3. **Development Patterns**:
   - Template design patterns for modular prompt construction
   - Context management architectures for conversation systems
   - Token optimization strategies for cost efficiency

## Conclusion

The WebContentAnalyzer's prompt engineering implementation is functional but basic, scoring 5/10 on our evaluation scale. While the current approach provides adequate results for simple analyses, it lacks the sophistication needed for advanced AI interactions, cost optimization, and production-ready features.

By implementing the recommended improvements, especially the structured template management, token optimization, and safety enhancements, the application could achieve significantly better AI analysis results while improving efficiency and reducing costs. The context-aware prompting and dynamic prompt generation would further enhance the application's capabilities, allowing for more personalized and effective content analysis.

The provided implementation examples offer a clear path to elevate the application's prompt engineering from adequate to excellent, positioning it for enhanced scalability, flexibility, and advanced AI capabilities.
