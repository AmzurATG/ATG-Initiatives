# WebContentAnalyzer - Data Architecture Review

## Overview

This document provides a detailed architectural assessment of the WebContentAnalyzer data architecture, focusing on data models, data flow patterns, transformation strategies, and persistence mechanisms.

## Data Architecture Score: 7/10 (GOOD)

The WebContentAnalyzer demonstrates a functional data architecture that effectively handles web content extraction, transformation, and analysis. The data flow from URL input to analysis results is well-structured, with appropriate data transformation steps. While the application doesn't use a traditional database, it implements reasonable data handling patterns for its domain. The architecture would benefit from more formal data modeling, enhanced validation, and structured persistence strategies.

## Data Model Design Assessment

### Implicit Data Models

The application primarily uses dictionary-based data structures rather than formal class-based models, which provides flexibility but reduces type safety and validation:

```python
# Implicit data model for analysis results
analysis_result = {
    "url": url,
    "timestamp": datetime.now().isoformat(),
    "title": extracted_content.get("title", ""),
    "content_summary": content_summary,
    "topics": topics,
    "sentiment": sentiment_analysis,
    "metadata": metadata
}
```

**Data Model Recommendations:**

A more explicit data modeling approach would enhance type safety and validation:

```python
from pydantic import BaseModel, HttpUrl, Field
from datetime import datetime
from typing import List, Dict, Optional, Any

class ContentMetadata(BaseModel):
    """Metadata about the extracted content."""
    word_count: int = 0
    reading_time_minutes: float = 0
    language: Optional[str] = None
    author: Optional[str] = None
    publish_date: Optional[datetime] = None
    source_domain: str
    tags: List[str] = Field(default_factory=list)

class SentimentAnalysis(BaseModel):
    """Sentiment analysis result."""
    score: float  # Between 0 (negative) and 1 (positive)
    label: str  # "Positive", "Neutral", or "Negative"
    confidence: float  # Between 0 and 1
    details: Optional[Dict[str, Any]] = None

class ContentAnalysis(BaseModel):
    """Analysis result for web content."""
    url: HttpUrl
    timestamp: datetime = Field(default_factory=datetime.now)
    title: str = ""
    content_summary: str
    topics: List[str] = Field(default_factory=list)
    sentiment: Optional[SentimentAnalysis] = None
    metadata: ContentMetadata
    
    class Config:
        json_encoders = {
            datetime: lambda v: v.isoformat()
        }

class ExtractedContent(BaseModel):
    """Raw extracted content from a web page."""
    url: HttpUrl
    html: str
    text: str
    title: Optional[str] = None
    metadata: Dict[str, Any] = Field(default_factory=dict)
    timestamp: datetime = Field(default_factory=datetime.now)
```

### Entity Relationship Design

Since the application doesn't use a database, traditional entity relationships aren't explicitly modeled. However, there are implicit relationships in the data:

1. URL → ExtractedContent → ContentAnalysis

**Entity Relationship Recommendations:**

```
URL (Primary Entity)
↓
ExtractedContent (Web content data)
↓
ContentAnalysis (Analysis results)
  ↓
  ├── Topics (List of identified topics)
  ├── Sentiment (Sentiment analysis results)
  └── Metadata (Content metadata information)
```

Even without a traditional database, these relationships could be formalized through class structures and references.

## Data Flow Architecture

### Data Extraction and Transformation Flow

The application implements a multi-step data flow for content extraction and analysis:

```
URL Input → HTML Fetching → Content Extraction → Text Processing → Analysis → Results
```

Current implementation:

```python
async def analyze_url(url):
    """Analyze content from a URL."""
    # Step 1: Fetch HTML content
    html_content = await fetch_url(url)
    
    # Step 2: Extract structured content from HTML
    extracted_content = extract_content(html_content)
    
    # Step 3: Process and analyze the text content
    text_content = extracted_content.get("text", "")
    
    # Step 4: Perform various analyses
    content_summary = generate_summary(text_content)
    topics = extract_topics(text_content)
    sentiment_analysis = analyze_sentiment(text_content)
    
    # Step 5: Gather metadata
    metadata = extract_metadata(html_content, url)
    
    # Step 6: Compile results
    analysis_result = {
        "url": url,
        "timestamp": datetime.now().isoformat(),
        "title": extracted_content.get("title", ""),
        "content_summary": content_summary,
        "topics": topics,
        "sentiment": sentiment_analysis,
        "metadata": metadata
    }
    
    return analysis_result
```

**Data Flow Architecture Improvements:**

A more pipeline-oriented architecture with explicit transformation steps:

```python
class ContentProcessingPipeline:
    """Pipeline for processing web content from URL to analysis."""
    
    def __init__(self, extractors=None, analyzers=None, config=None):
        self.content_extractor = extractors.get("content") or WebContentExtractor()
        self.text_processor = extractors.get("text") or TextProcessor()
        self.summary_analyzer = analyzers.get("summary") or SummaryAnalyzer()
        self.topic_analyzer = analyzers.get("topics") or TopicAnalyzer()
        self.sentiment_analyzer = analyzers.get("sentiment") or SentimentAnalyzer()
        self.metadata_extractor = extractors.get("metadata") or MetadataExtractor()
        self.config = config or {}
        
    async def process(self, url: str) -> ContentAnalysis:
        """Process a URL through the entire pipeline."""
        try:
            # Step 1: Extract content
            extracted_content = await self.content_extractor.extract(url)
            
            # Step 2: Process text
            processed_text = self.text_processor.process(extracted_content.text)
            
            # Step 3: Analyze content
            summary = self.summary_analyzer.analyze(processed_text)
            topics = self.topic_analyzer.analyze(processed_text)
            sentiment = self.sentiment_analyzer.analyze(processed_text)
            metadata = self.metadata_extractor.extract(extracted_content)
            
            # Step 4: Construct result
            return ContentAnalysis(
                url=url,
                title=extracted_content.title,
                content_summary=summary,
                topics=topics,
                sentiment=sentiment,
                metadata=metadata
            )
            
        except Exception as e:
            # Proper error handling with specific exception types
            raise ContentProcessingError(f"Error processing URL {url}: {str(e)}")
```

### Data Transformation Patterns

The application performs several data transformations:

1. HTML to structured text extraction
2. Text to summary generation
3. Text to topic extraction
4. Text to sentiment analysis

**Current Data Transformation Implementation:**

```python
def extract_content(html_content, url=None):
    """Extract structured content from HTML."""
    soup = BeautifulSoup(html_content, 'html.parser')
    
    # Extract title
    title = soup.title.string if soup.title else ""
    
    # Extract main content
    content = []
    for paragraph in soup.find_all(['p', 'h1', 'h2', 'h3', 'h4']):
        text = paragraph.get_text().strip()
        if text:
            content.append(text)
    
    # Join content into text
    text_content = "\n\n".join(content)
    
    return {
        "title": title,
        "text": text_content,
        "url": url
    }
```

**Improved Data Transformation Architecture:**

```python
class ContentExtractor:
    """Extracts structured content from HTML."""
    
    def __init__(self, config=None):
        self.config = config or {}
        
    def extract(self, html: str, url: Optional[str] = None) -> ExtractedContent:
        """Extract structured content from HTML."""
        soup = BeautifulSoup(html, 'html.parser')
        
        # Extract title with fallback
        title = self._extract_title(soup)
        
        # Extract main content with content detection heuristics
        text = self._extract_main_content(soup)
        
        # Extract metadata
        metadata = self._extract_metadata(soup, url)
        
        return ExtractedContent(
            url=url or "",
            html=html,
            text=text,
            title=title,
            metadata=metadata
        )
        
    def _extract_title(self, soup: BeautifulSoup) -> str:
        """Extract page title with fallbacks."""
        if soup.title and soup.title.string:
            return soup.title.string.strip()
        
        # Fallback to h1
        h1 = soup.find('h1')
        if h1:
            return h1.get_text().strip()
            
        return ""
        
    def _extract_main_content(self, soup: BeautifulSoup) -> str:
        """Extract main content using heuristics to find content area."""
        # Try to find main content containers
        main_content = soup.find(['main', 'article', '[role=main]'])
        
        if main_content:
            paragraphs = main_content.find_all(['p', 'h1', 'h2', 'h3', 'h4'])
        else:
            # Fallback to all paragraphs and headings
            paragraphs = soup.find_all(['p', 'h1', 'h2', 'h3', 'h4'])
        
        # Filter out navigation, footer, etc.
        content_paragraphs = [
            p for p in paragraphs 
            if not self._is_in_navigation_or_footer(p) and len(p.get_text().strip()) > 20
        ]
        
        # Extract and join text
        content = [p.get_text().strip() for p in content_paragraphs]
        return "\n\n".join(content)
        
    def _is_in_navigation_or_footer(self, element) -> bool:
        """Check if element is inside navigation or footer."""
        parents = element.find_parents()
        for parent in parents:
            if parent.name in ['nav', 'footer', 'header']:
                return True
            if parent.get('role') in ['navigation', 'banner', 'contentinfo']:
                return True
        return False
        
    def _extract_metadata(self, soup: BeautifulSoup, url: Optional[str]) -> Dict[str, Any]:
        """Extract metadata from HTML."""
        metadata = {}
        
        # Extract OpenGraph metadata
        og_title = soup.find('meta', property='og:title')
        if og_title:
            metadata['og_title'] = og_title.get('content', '')
            
        og_description = soup.find('meta', property='og:description')
        if og_description:
            metadata['og_description'] = og_description.get('content', '')
            
        # Extract author
        author = soup.find('meta', attrs={'name': 'author'})
        if author:
            metadata['author'] = author.get('content', '')
            
        # Extract publish date
        date = soup.find('meta', property='article:published_time')
        if date:
            metadata['publish_date'] = date.get('content', '')
            
        # Extract domain from URL
        if url:
            parsed_url = urlparse(url)
            metadata['domain'] = parsed_url.netloc
            
        return metadata
```

## Data Persistence Architecture

### File-Based Data Storage

The WebContentAnalyzer uses simple file-based storage for persisting analysis history:

```python
def save_analysis_history(entry):
    """Save analysis history entry to file."""
    history_file = "analysis_history.json"
    
    try:
        # Read existing history
        if os.path.exists(history_file):
            with open(history_file, "r") as f:
                history = json.load(f)
        else:
            history = []
        
        # Add timestamp if not present
        if "timestamp" not in entry:
            entry["timestamp"] = datetime.now().isoformat()
        
        # Add entry to history
        history.append(entry)
        
        # Write updated history
        with open(history_file, "w") as f:
            json.dump(history, f, indent=2)
            
        return {"status": "success"}
    except Exception as e:
        return {"status": "error", "message": str(e)}
```

**Data Persistence Architecture Improvements:**

A more robust data storage abstraction:

```python
class AnalysisRepository:
    """Repository for storing and retrieving analysis results."""
    
    def __init__(self, storage_path="./data", storage_format="json"):
        self.storage_path = storage_path
        self.storage_format = storage_format
        self.history_file = os.path.join(storage_path, "analysis_history.json")
        
        # Ensure storage directory exists
        os.makedirs(storage_path, exist_ok=True)
        
    async def save_analysis(self, analysis: ContentAnalysis) -> str:
        """Save analysis result and return its ID."""
        # Convert to dictionary
        analysis_dict = analysis.dict()
        
        # Add unique ID if not present
        if "id" not in analysis_dict:
            analysis_dict["id"] = str(uuid.uuid4())
        
        # Read existing history
        history = await self._load_history()
        
        # Add to history
        history.append(analysis_dict)
        
        # Write updated history
        await self._save_history(history)
        
        return analysis_dict["id"]
        
    async def get_analysis(self, analysis_id: str) -> Optional[ContentAnalysis]:
        """Get analysis by ID."""
        history = await self._load_history()
        
        for entry in history:
            if entry.get("id") == analysis_id:
                return ContentAnalysis(**entry)
                
        return None
        
    async def get_all_analyses(self) -> List[ContentAnalysis]:
        """Get all analyses."""
        history = await self._load_history()
        return [ContentAnalysis(**entry) for entry in history]
        
    async def delete_analysis(self, analysis_id: str) -> bool:
        """Delete analysis by ID."""
        history = await self._load_history()
        
        for i, entry in enumerate(history):
            if entry.get("id") == analysis_id:
                del history[i]
                await self._save_history(history)
                return True
                
        return False
        
    async def _load_history(self) -> List[Dict[str, Any]]:
        """Load analysis history from file."""
        try:
            if os.path.exists(self.history_file):
                async with aiofiles.open(self.history_file, "r") as f:
                    content = await f.read()
                    return json.loads(content)
            return []
        except Exception as e:
            logger.error(f"Error loading history: {str(e)}")
            return []
            
    async def _save_history(self, history: List[Dict[str, Any]]) -> None:
        """Save analysis history to file."""
        try:
            async with aiofiles.open(self.history_file, "w") as f:
                content = json.dumps(history, indent=2)
                await f.write(content)
        except Exception as e:
            logger.error(f"Error saving history: {str(e)}")
            raise StorageError(f"Failed to save history: {str(e)}")
```

## Data Validation Architecture

### Current Data Validation

The WebContentAnalyzer implements basic data validation, primarily focused on URL validation:

```python
def is_valid_url(url):
    """Validate URL format and security."""
    if not url:
        return False
    
    try:
        result = urlparse(url)
        if not all([result.scheme, result.netloc]):
            return False
        
        # Check if scheme is HTTP or HTTPS
        if result.scheme not in ['http', 'https']:
            return False
            
        # Prevent private IPs
        hostname = result.netloc.split(':')[0]
        try:
            ip = socket.gethostbyname(hostname)
            if is_private_ip(ip):
                return False
        except socket.gaierror:
            pass
            
        return True
    except:
        return False
```

**Data Validation Architecture Improvements:**

A more comprehensive data validation framework:

```python
class DataValidator:
    """Comprehensive data validation framework."""
    
    def __init__(self, config=None):
        self.config = config or {}
        
    def validate_url(self, url: str) -> Tuple[bool, Optional[str]]:
        """Validate URL and return (is_valid, error_message)."""
        if not url:
            return False, "URL cannot be empty"
        
        try:
            # Parse URL
            result = urlparse(url)
            
            # Check scheme and netloc
            if not all([result.scheme, result.netloc]):
                return False, "Invalid URL format"
            
            # Check if scheme is HTTP or HTTPS
            if result.scheme not in ['http', 'https']:
                return False, "URL must use HTTP or HTTPS protocol"
                
            # Prevent localhost
            hostname = result.netloc.split(':')[0]
            if hostname.lower() in ['localhost', '127.0.0.1', '::1']:
                return False, "Local URLs not allowed"
                
            # Prevent private IPs
            try:
                ip = socket.gethostbyname(hostname)
                if self._is_private_ip(ip):
                    return False, "Private IP addresses not allowed"
            except socket.gaierror:
                # Not an IP, continue with domain validation
                pass
                
            # Domain validation
            if self.config.get("check_domain_allowed", False):
                if not self._is_domain_allowed(hostname):
                    return False, f"Domain {hostname} is not on the allowed list"
                    
            return True, None
        except Exception as e:
            return False, f"URL validation error: {str(e)}"
            
    def validate_content(self, content: Dict[str, Any]) -> Tuple[bool, Optional[str]]:
        """Validate extracted content structure."""
        if not content:
            return False, "Content cannot be empty"
            
        # Check required fields
        required_fields = ['text', 'title']
        missing_fields = [field for field in required_fields if field not in content]
        
        if missing_fields:
            return False, f"Missing required content fields: {', '.join(missing_fields)}"
            
        # Check text content
        if not content.get('text') or len(content.get('text', '')) < 10:
            return False, "Text content too short or empty"
            
        return True, None
        
    def validate_analysis_result(self, result: Dict[str, Any]) -> Tuple[bool, Optional[str]]:
        """Validate analysis result structure."""
        if not result:
            return False, "Analysis result cannot be empty"
            
        # Check required fields
        required_fields = ['url', 'content_summary', 'topics']
        missing_fields = [field for field in required_fields if field not in result]
        
        if missing_fields:
            return False, f"Missing required result fields: {', '.join(missing_fields)}"
            
        # Validate topics
        if not isinstance(result.get('topics', []), list):
            return False, "Topics must be a list"
            
        # Validate sentiment if present
        sentiment = result.get('sentiment')
        if sentiment:
            if not isinstance(sentiment, dict):
                return False, "Sentiment must be an object"
            if 'score' in sentiment and not isinstance(sentiment['score'], (int, float)):
                return False, "Sentiment score must be a number"
                
        return True, None
        
    def _is_private_ip(self, ip: str) -> bool:
        """Check if IP address is private."""
        try:
            ip_obj = ipaddress.ip_address(ip)
            return ip_obj.is_private
        except ValueError:
            return False
            
    def _is_domain_allowed(self, domain: str) -> bool:
        """Check if domain is on allowed list or not on blocklist."""
        allowed_domains = self.config.get("allowed_domains", [])
        blocked_domains = self.config.get("blocked_domains", [])
        
        # If allowed domains specified, domain must be in the list
        if allowed_domains:
            return domain in allowed_domains or any(domain.endswith(f".{d}") for d in allowed_domains)
            
        # If blocked domains specified, domain must not be in the list
        if blocked_domains:
            return domain not in blocked_domains and not any(domain.endswith(f".{d}") for d in blocked_domains)
            
        # If no restrictions, domain is allowed
        return True
```

## Data Processing Architecture

### Content Chunking and Processing

The WebContentAnalyzer implements content chunking for processing large texts:

```python
def chunk_text(text, max_chunk_size=4000):
    """Split text into chunks of max_chunk_size."""
    if len(text) <= max_chunk_size:
        return [text]
    
    # Split by paragraphs
    paragraphs = text.split("\n\n")
    chunks = []
    current_chunk = ""
    
    for paragraph in paragraphs:
        if len(current_chunk) + len(paragraph) <= max_chunk_size:
            current_chunk += paragraph + "\n\n"
        else:
            chunks.append(current_chunk)
            current_chunk = paragraph + "\n\n"
    
    if current_chunk:
        chunks.append(current_chunk)
    
    return chunks
```

**Improved Data Processing Architecture:**

```python
class ContentProcessor:
    """Content processing and chunking logic."""
    
    def __init__(self, config=None):
        self.config = config or {}
        self.max_chunk_size = self.config.get("max_chunk_size", 4000)
        self.overlap_size = self.config.get("overlap_size", 200)
        
    def process_content(self, content: ExtractedContent) -> ProcessedContent:
        """Process extracted content."""
        # Normalize text
        normalized_text = self._normalize_text(content.text)
        
        # Detect language
        language = self._detect_language(normalized_text)
        
        # Extract important sections
        sections = self._extract_sections(normalized_text)
        
        # Generate chunks
        chunks = self._generate_smart_chunks(normalized_text, sections)
        
        return ProcessedContent(
            url=content.url,
            title=content.title,
            original_text=content.text,
            normalized_text=normalized_text,
            language=language,
            sections=sections,
            chunks=chunks,
            metadata=content.metadata
        )
        
    def _normalize_text(self, text: str) -> str:
        """Normalize text by cleaning whitespace and special characters."""
        # Remove excessive whitespace
        text = re.sub(r'\s+', ' ', text)
        
        # Remove special characters
        text = re.sub(r'[^\w\s.,;:!?\'\"()[\]{}]', '', text)
        
        return text.strip()
        
    def _detect_language(self, text: str) -> str:
        """Detect language of the text."""
        try:
            import langdetect
            return langdetect.detect(text)
        except Exception:
            # Default to English if detection fails
            return "en"
            
    def _extract_sections(self, text: str) -> List[Dict[str, str]]:
        """Extract sections from text based on headings."""
        # Simple heading pattern
        heading_pattern = r'(^|\n)(#{1,6}\s+.+?|[A-Z][A-Za-z\s]+:)(\n|$)'
        
        matches = re.finditer(heading_pattern, text)
        sections = []
        last_pos = 0
        
        for match in matches:
            # Extract heading
            heading = match.group(2).strip()
            
            # Get content between headings
            if last_pos > 0:
                section_content = text[last_pos:match.start()].strip()
                sections.append({
                    "heading": sections[-1]["heading"],
                    "content": section_content
                })
                
            last_pos = match.end()
            sections.append({
                "heading": heading,
                "content": ""
            })
            
        # Add final section content
        if last_pos < len(text) and sections:
            sections[-1]["content"] = text[last_pos:].strip()
            
        return sections
        
    def _generate_smart_chunks(self, text: str, sections: List[Dict[str, str]]) -> List[str]:
        """Generate smart chunks from text, preserving section boundaries."""
        if len(text) <= self.max_chunk_size:
            return [text]
            
        chunks = []
        
        if not sections:
            # Fall back to paragraph-based chunking if no sections
            return self._chunk_by_paragraphs(text)
            
        current_chunk = ""
        current_section_heading = None
        
        for section in sections:
            heading = section["heading"]
            content = section["content"]
            
            # Check if adding this section would exceed chunk size
            section_text = f"{heading}\n\n{content}\n\n"
            
            if len(current_chunk) + len(section_text) <= self.max_chunk_size:
                # Add section to current chunk
                current_chunk += section_text
                current_section_heading = heading
            else:
                # Finalize current chunk
                if current_chunk:
                    chunks.append(current_chunk)
                    
                # Start new chunk
                if len(section_text) > self.max_chunk_size:
                    # Section itself is too large, need to split it
                    sub_chunks = self._chunk_by_paragraphs(content)
                    
                    # Add heading to each sub-chunk
                    for i, sub_chunk in enumerate(sub_chunks):
                        chunk_title = f"{heading} (Part {i+1}/{len(sub_chunks)})\n\n"
                        chunks.append(f"{chunk_title}{sub_chunk}")
                else:
                    current_chunk = section_text
                    current_section_heading = heading
                    
        # Add final chunk
        if current_chunk:
            chunks.append(current_chunk)
            
        return chunks
        
    def _chunk_by_paragraphs(self, text: str) -> List[str]:
        """Split text into chunks by paragraphs."""
        paragraphs = re.split(r'\n\s*\n', text)
        chunks = []
        current_chunk = ""
        
        for paragraph in paragraphs:
            paragraph = paragraph.strip() + "\n\n"
            
            if len(current_chunk) + len(paragraph) <= self.max_chunk_size:
                current_chunk += paragraph
            else:
                # If current paragraph is too large alone
                if not current_chunk and len(paragraph) > self.max_chunk_size:
                    # Split by sentences
                    sentences = re.split(r'(?<=[.!?])\s+', paragraph)
                    sent_chunk = ""
                    
                    for sentence in sentences:
                        if len(sent_chunk) + len(sentence) <= self.max_chunk_size:
                            sent_chunk += sentence + " "
                        else:
                            if sent_chunk:
                                chunks.append(sent_chunk.strip())
                            sent_chunk = sentence + " "
                    
                    if sent_chunk:
                        chunks.append(sent_chunk.strip())
                else:
                    chunks.append(current_chunk.strip())
                    current_chunk = paragraph
        
        if current_chunk:
            chunks.append(current_chunk.strip())
            
        return chunks
```

## Data Security Architecture

### Current Data Security Implementation

The WebContentAnalyzer implements basic security measures for URL validation:

```python
def is_private_ip(ip):
    """Check if IP address is private."""
    try:
        return ipaddress.ip_address(ip).is_private
    except ValueError:
        return False
```

**Enhanced Data Security Architecture:**

```python
class DataSecurityManager:
    """Manages data security aspects of content analysis."""
    
    def __init__(self, config=None):
        self.config = config or {}
        self.safe_browsing_api_key = self.config.get("google_safe_browsing_api_key")
        
    def validate_url_security(self, url: str) -> Tuple[bool, Optional[str]]:
        """Validate URL security and return (is_safe, threat_reason)."""
        # Check URL format
        if not self._is_valid_url_format(url):
            return False, "Invalid URL format"
            
        # Check for private/internal IPs
        if self._is_internal_ip_url(url):
            return False, "Internal/private IP addresses not allowed"
            
        # Check blocklist
        if self._is_on_blocklist(url):
            return False, "URL is on security blocklist"
            
        # Check Google Safe Browsing if configured
        if self.safe_browsing_api_key and self.config.get("check_safe_browsing", False):
            is_safe, threat = self._check_safe_browsing(url)
            if not is_safe:
                return False, f"URL flagged as unsafe: {threat}"
                
        return True, None
        
    def sanitize_content(self, content: str) -> str:
        """Sanitize extracted content for security."""
        # Remove potentially dangerous HTML
        if self.config.get("remove_html", True):
            content = self._strip_html(content)
            
        # Remove suspicious patterns
        if self.config.get("remove_suspicious_patterns", True):
            content = self._remove_suspicious_patterns(content)
            
        return content
        
    def protect_pii(self, text: str) -> str:
        """Identify and mask PII in extracted content."""
        if not self.config.get("pii_protection_enabled", False):
            return text
            
        # Mask emails
        if self.config.get("mask_emails", True):
            text = re.sub(r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b', '[EMAIL REDACTED]', text)
            
        # Mask phone numbers
        if self.config.get("mask_phone_numbers", True):
            text = re.sub(r'\b(\+\d{1,3}\s?)?\(?\d{3}\)?[\s.-]?\d{3}[\s.-]?\d{4}\b', '[PHONE REDACTED]', text)
            
        # Mask credit card numbers
        if self.config.get("mask_credit_cards", True):
            text = re.sub(r'\b(?:\d{4}[- ]?){3}\d{4}\b', '[CREDIT CARD REDACTED]', text)
            
        # Mask SSNs
        if self.config.get("mask_ssn", True):
            text = re.sub(r'\b\d{3}-\d{2}-\d{4}\b', '[SSN REDACTED]', text)
            
        return text
        
    def _is_valid_url_format(self, url: str) -> bool:
        """Check if URL has valid format."""
        try:
            result = urlparse(url)
            return all([result.scheme, result.netloc]) and result.scheme in ['http', 'https']
        except Exception:
            return False
            
    def _is_internal_ip_url(self, url: str) -> bool:
        """Check if URL points to internal/private IP."""
        try:
            result = urlparse(url)
            hostname = result.netloc.split(':')[0]
            
            # Check localhost
            if hostname.lower() in ['localhost', '127.0.0.1', '::1']:
                return True
                
            # Check private IP
            try:
                ip = socket.gethostbyname(hostname)
                return ipaddress.ip_address(ip).is_private
            except (socket.gaierror, ValueError):
                return False
        except Exception:
            return True  # Default to safe option on error
            
    def _is_on_blocklist(self, url: str) -> bool:
        """Check if URL domain is on blocklist."""
        try:
            domain = urlparse(url).netloc.lower()
            blocklist = self.config.get("domain_blocklist", [])
            return domain in blocklist or any(domain.endswith(f".{blocked}") for blocked in blocklist)
        except Exception:
            return False
            
    def _check_safe_browsing(self, url: str) -> Tuple[bool, Optional[str]]:
        """Check URL against Google Safe Browsing API."""
        if not self.safe_browsing_api_key:
            return True, None
            
        try:
            import requests
            
            api_url = f"https://safebrowsing.googleapis.com/v4/threatMatches:find?key={self.safe_browsing_api_key}"
            payload = {
                "client": {
                    "clientId": "webcontent-analyzer",
                    "clientVersion": "1.0.0"
                },
                "threatInfo": {
                    "threatTypes": ["MALWARE", "SOCIAL_ENGINEERING", "UNWANTED_SOFTWARE", "POTENTIALLY_HARMFUL_APPLICATION"],
                    "platformTypes": ["ANY_PLATFORM"],
                    "threatEntryTypes": ["URL"],
                    "threatEntries": [{"url": url}]
                }
            }
            
            response = requests.post(api_url, json=payload)
            result = response.json()
            
            if "matches" in result:
                threat_type = result["matches"][0].get("threatType", "UNKNOWN_THREAT")
                return False, threat_type
                
            return True, None
        except Exception as e:
            # Log error but don't block URL on API failure
            logging.error(f"Safe Browsing API error: {str(e)}")
            return True, None
            
    def _strip_html(self, content: str) -> str:
        """Remove HTML tags from content."""
        return re.sub(r'<[^>]*>', '', content)
        
    def _remove_suspicious_patterns(self, content: str) -> str:
        """Remove suspicious patterns like script blocks, iframes, etc."""
        # Remove script-like content
        content = re.sub(r'(javascript:|eval\(|document\.write\()', '', content, flags=re.IGNORECASE)
        
        # Remove iframe-like content
        content = re.sub(r'<iframe.*?>.*?</iframe>', '', content, flags=re.DOTALL | re.IGNORECASE)
        
        # Remove on* attributes (onclick, onload, etc.)
        content = re.sub(r' on\w+=".*?"', '', content, flags=re.IGNORECASE)
        
        return content
```

## Data Analysis Pipeline Architecture

### Current LLM Analysis Implementation

The WebContentAnalyzer uses LLMs for content analysis:

```python
async def analyze_with_llm(content, prompt_template):
    """Analyze content using LLM."""
    try:
        # Create prompt from template and content
        prompt = prompt_template.replace("{content}", content)
        
        # Call OpenAI API
        response = await openai.ChatCompletion.acreate(
            model="gpt-3.5-turbo",
            messages=[
                {"role": "system", "content": "You are a helpful assistant that analyzes web content."},
                {"role": "user", "content": prompt}
            ],
            temperature=0.7,
            max_tokens=1000,
            n=1,
            stop=None
        )
        
        # Extract response
        analysis = response.choices[0].message.content.strip()
        return analysis
    except Exception as e:
        logging.error(f"LLM analysis error: {str(e)}")
        raise AnalysisError(f"LLM analysis failed: {str(e)}")
```

**Enhanced Data Analysis Pipeline Architecture:**

```python
class AnalysisPipeline:
    """Pipeline for analyzing content using LLMs."""
    
    def __init__(self, config=None):
        self.config = config or {}
        self.openai_api_key = self.config.get("openai_api_key")
        self.gemini_api_key = self.config.get("gemini_api_key")
        self.model = self.config.get("llm_model", "gpt-4o")
        self.fallback_model = self.config.get("fallback_model", "gemini-pro")
        self.max_retries = self.config.get("max_retries", 2)
        self.chunk_size = self.config.get("max_chunk_size", 4000)
        
    async def analyze_content(self, processed_content: ProcessedContent) -> ContentAnalysis:
        """Run complete analysis pipeline on processed content."""
        # Set up processing context
        context = {
            "url": processed_content.url,
            "title": processed_content.title,
            "language": processed_content.language,
            "chunks": processed_content.chunks,
            "metadata": processed_content.metadata
        }
        
        # Process content chunks
        chunk_results = []
        for i, chunk in enumerate(processed_content.chunks):
            try:
                # Analyze chunk
                chunk_analysis = await self._analyze_chunk(
                    chunk=chunk,
                    chunk_index=i,
                    total_chunks=len(processed_content.chunks),
                    context=context
                )
                chunk_results.append(chunk_analysis)
            except Exception as e:
                # Log error but continue with other chunks
                logging.error(f"Error analyzing chunk {i}: {str(e)}")
                # Add placeholder for failed chunk
                chunk_results.append({
                    "error": str(e),
                    "chunk_index": i
                })
                
        # Aggregate results from chunks
        aggregated_analysis = self._aggregate_chunk_results(chunk_results, context)
        
        # Create final analysis result
        return ContentAnalysis(
            url=processed_content.url,
            timestamp=datetime.now(),
            title=processed_content.title,
            content_summary=aggregated_analysis.get("summary", ""),
            topics=aggregated_analysis.get("topics", []),
            sentiment=SentimentAnalysis(
                score=aggregated_analysis.get("sentiment", {}).get("score", 0.5),
                label=aggregated_analysis.get("sentiment", {}).get("label", "Neutral"),
                confidence=aggregated_analysis.get("sentiment", {}).get("confidence", 0.5)
            ) if "sentiment" in aggregated_analysis else None,
            metadata=ContentMetadata(
                word_count=aggregated_analysis.get("metadata", {}).get("word_count", 0),
                reading_time_minutes=aggregated_analysis.get("metadata", {}).get("reading_time_minutes", 0),
                language=processed_content.language,
                source_domain=processed_content.metadata.get("domain", "")
            )
        )
        
    async def _analyze_chunk(self, chunk: str, chunk_index: int, total_chunks: int, context: Dict[str, Any]) -> Dict[str, Any]:
        """Analyze a single content chunk using LLM."""
        # Create analysis prompt
        prompt = self._create_analysis_prompt(chunk, chunk_index, total_chunks, context)
        
        # Try primary model first
        try:
            return await self._analyze_with_openai(prompt, chunk_index)
        except Exception as e:
            logging.warning(f"OpenAI analysis failed for chunk {chunk_index}: {str(e)}")
            
            # Try fallback model if available
            if self.gemini_api_key:
                try:
                    return await self._analyze_with_gemini(prompt, chunk_index)
                except Exception as e2:
                    logging.error(f"Gemini fallback also failed for chunk {chunk_index}: {str(e2)}")
                    raise AnalysisError(f"All LLM providers failed for chunk {chunk_index}: {str(e)} | {str(e2)}")
            else:
                raise AnalysisError(f"LLM analysis failed and no fallback available: {str(e)}")
                
    async def _analyze_with_openai(self, prompt: str, chunk_index: int) -> Dict[str, Any]:
        """Analyze content using OpenAI API."""
        if not self.openai_api_key:
            raise ConfigError("OpenAI API key not configured")
            
        import openai
        openai.api_key = self.openai_api_key
        
        # Implement retry logic
        retry_count = 0
        while retry_count <= self.max_retries:
            try:
                response = await openai.ChatCompletion.acreate(
                    model=self.model,
                    messages=[
                        {"role": "system", "content": "You are a web content analysis expert."},
                        {"role": "user", "content": prompt}
                    ],
                    temperature=0.3,  # Lower temperature for more consistent analysis
                    max_tokens=1500,
                    n=1,
                    response_format={"type": "json_object"}  # Request JSON response
                )
                
                # Log security warning about LLM outputs
                logging.warning(f"LLM output for chunk {chunk_index+1} may hallucinate or misinterpret. Review results critically.")
                
                # Extract and parse JSON response
                analysis_text = response.choices[0].message.content.strip()
                return self._parse_llm_response(analysis_text)
                
            except openai.error.RateLimitError as e:
                # Handle rate limits with exponential backoff
                retry_count += 1
                if retry_count <= self.max_retries:
                    wait_time = 2 ** (retry_count - 1)  # Exponential backoff
                    logging.warning(f"OpenAI rate limit hit for chunk {chunk_index+1}, retrying in {wait_time}s (attempt {retry_count}/{self.max_retries})")
                    await asyncio.sleep(wait_time)
                else:
                    raise AnalysisError(f"OpenAI rate limit exceeded after {self.max_retries} retries")
            except Exception as e:
                # Don't retry on other errors
                raise AnalysisError(f"OpenAI API error: {str(e)}")
                
    async def _analyze_with_gemini(self, prompt: str, chunk_index: int) -> Dict[str, Any]:
        """Analyze content using Google Gemini API."""
        if not self.gemini_api_key:
            raise ConfigError("Gemini API key not configured")
            
        import google.generativeai as genai
        genai.configure(api_key=self.gemini_api_key)
        
        try:
            model = genai.GenerativeModel(self.fallback_model)
            response = await model.generate_content_async(prompt)
            
            # Log security warning about LLM outputs
            logging.warning(f"LLM output for chunk {chunk_index+1} may hallucinate or misinterpret. Review results critically.")
            
            # Extract and parse response
            analysis_text = response.text
            return self._parse_llm_response(analysis_text)
            
        except Exception as e:
            raise AnalysisError(f"Gemini API error: {str(e)}")
            
    def _parse_llm_response(self, response_text: str) -> Dict[str, Any]:
        """Parse LLM response text into structured data."""
        # Try to extract JSON from response
        try:
            # Look for JSON object in the response
            json_match = re.search(r'```json\n(.*?)\n```|^\s*({.*})\s*$', response_text, re.DOTALL)
            if json_match:
                json_str = json_match.group(1) or json_match.group(2)
                return json.loads(json_str)
                
            # If no JSON found, try parsing the whole response
            return json.loads(response_text)
            
        except json.JSONDecodeError:
            # If JSON parsing fails, return text as summary
            return {
                "summary": response_text,
                "topics": [],
                "sentiment": {
                    "label": "Neutral",
                    "score": 0.5
                }
            }
            
    def _create_analysis_prompt(self, chunk: str, chunk_index: int, total_chunks: int, context: Dict[str, Any]) -> str:
        """Create analysis prompt for LLM."""
        # Basic context information
        context_info = f"URL: {context['url']}\n"
        context_info += f"Title: {context['title']}\n"
        context_info += f"Language: {context['language']}\n"
        context_info += f"Chunk: {chunk_index + 1} of {total_chunks}\n\n"
        
        # Task description
        task = (
            "Analyze the following web content and provide a structured analysis "
            "in JSON format with the following fields:\n"
            "- summary: A concise summary of the content\n"
            "- key_points: An array of key points from the content\n"
            "- topics: An array of main topics covered\n"
            "- sentiment: An object with 'label' (Positive/Neutral/Negative) and 'score' (0-1)\n"
            "- readability: An object with 'score' (0-100) and 'comment'\n"
            "- seo_analysis: Brief SEO assessment including keyword density\n"
        )
        
        # Content to analyze
        content_section = f"CONTENT TO ANALYZE:\n\n{chunk}"
        
        # Special instructions based on context
        special_instructions = ""
        if chunk_index > 0:
            special_instructions += "\nNote: This is a continuation from previous content. Focus on new information not covered in earlier sections."
        
        if total_chunks > 1:
            special_instructions += f"\nThis is part {chunk_index + 1} of {total_chunks} from the same document."
            
        # Format instructions
        format_instructions = (
            "Return your analysis as a valid JSON object with the fields described above. "
            "Ensure your JSON is properly formatted with quotes around keys and string values."
        )
        
        # Assemble final prompt
        prompt = f"{context_info}\n{task}\n\n{content_section}\n{special_instructions}\n\n{format_instructions}"
        return prompt
        
    def _aggregate_chunk_results(self, chunk_results: List[Dict[str, Any]], context: Dict[str, Any]) -> Dict[str, Any]:
        """Aggregate results from multiple chunks into a unified analysis."""
        # Initialize aggregate structure
        aggregate = {
            "summary": "",
            "key_points": [],
            "topics": [],
            "sentiment": {
                "positive": 0,
                "neutral": 0,
                "negative": 0,
                "scores": []
            },
            "metadata": {
                "word_count": 0,
                "reading_time_minutes": 0
            }
        }
        
        valid_chunks = [c for c in chunk_results if "error" not in c]
        
        # Skip aggregation if no valid chunks
        if not valid_chunks:
            return {
                "summary": "Unable to analyze content due to errors.",
                "topics": [],
                "sentiment": {"label": "Neutral", "score": 0.5}
            }
            
        # Process each chunk result
        for result in valid_chunks:
            # Aggregate summaries
            if "summary" in result and result["summary"]:
                if not aggregate["summary"]:
                    aggregate["summary"] = result["summary"]
                else:
                    aggregate["summary"] += " " + result["summary"]
                    
            # Collect key points
            if "key_points" in result and result["key_points"]:
                aggregate["key_points"].extend(result["key_points"])
                
            # Collect topics with deduplication
            if "topics" in result and result["topics"]:
                for topic in result["topics"]:
                    if topic not in aggregate["topics"]:
                        aggregate["topics"].append(topic)
                        
            # Track sentiment votes
            if "sentiment" in result:
                sentiment = result["sentiment"]
                if isinstance(sentiment, dict):
                    label = sentiment.get("label", "").lower()
                    score = sentiment.get("score", 0.5)
                    
                    # Count sentiment labels
                    if label in ["positive", "negative", "neutral"]:
                        aggregate["sentiment"][label] += 1
                        
                    # Track scores for averaging
                    if isinstance(score, (int, float)):
                        aggregate["sentiment"]["scores"].append(score)
                        
            # Accumulate metadata
            if "metadata" in result:
                metadata = result["metadata"]
                if isinstance(metadata, dict):
                    # Sum word counts
                    if "word_count" in metadata and isinstance(metadata["word_count"], (int, float)):
                        aggregate["metadata"]["word_count"] += metadata["word_count"]
                        
                    # Sum reading time
                    if "reading_time_minutes" in metadata and isinstance(metadata["reading_time_minutes"], (int, float)):
                        aggregate["metadata"]["reading_time_minutes"] += metadata["reading_time_minutes"]
        
        # Process aggregated data into final result
        
        # Determine majority sentiment
        sentiment_counts = {k: v for k, v in aggregate["sentiment"].items() if k in ["positive", "neutral", "negative"]}
        majority_sentiment = max(sentiment_counts.items(), key=lambda x: x[1])[0] if sentiment_counts else "neutral"
        
        # Calculate average sentiment score
        scores = aggregate["sentiment"].get("scores", [])
        avg_score = sum(scores) / len(scores) if scores else 0.5
        
        # Limit topics to top 10
        if len(aggregate["topics"]) > 10:
            aggregate["topics"] = aggregate["topics"][:10]
            
        # Limit key points to top 8
        if len(aggregate["key_points"]) > 8:
            aggregate["key_points"] = aggregate["key_points"][:8]
            
        # Truncate summary if too long
        if len(aggregate["summary"]) > 2000:
            aggregate["summary"] = aggregate["summary"][:1997] + "..."
            
        # Create final output structure
        return {
            "summary": aggregate["summary"],
            "key_points": aggregate["key_points"],
            "topics": aggregate["topics"],
            "sentiment": {
                "label": majority_sentiment.capitalize(),
                "score": avg_score
            },
            "metadata": aggregate["metadata"]
        }
```

## Data Architecture Recommendations

### Critical Data Architecture Improvements

1. **Implement Formal Data Models**
   - Create explicit Pydantic models for all data structures
   - Define clear data schemas and relationships
   - Implement validation at data boundaries

2. **Enhance Data Processing Pipeline**
   - Create a modular data processing pipeline architecture
   - Implement pluggable transformation components
   - Add robust error handling and recovery in data flow

3. **Improve Data Persistence Strategy**
   - Implement proper repository pattern
   - Add data versioning and history management
   - Create transaction boundaries for data operations

### High-Priority Data Improvements

1. **Data Validation Architecture**
   - Implement comprehensive validation at all data entry points
   - Create data schema evolution strategy
   - Add data integrity checks and constraints

2. **Data Security Enhancements**
   - Implement PII detection and masking
   - Enhance URL security validation
   - Add data access controls and audit trails

3. **Data Transformation Optimization**
   - Improve chunking algorithms for better content analysis
   - Create reusable data transformation components
   - Add intelligent content extraction heuristics

### Medium-Priority Data Improvements

1. **Metadata Management**
   - Enhance metadata extraction and structure
   - Add metadata validation and normalization
   - Create metadata search and filtering capabilities

2. **Content Classification**
   - Implement automated content type detection
   - Add language detection and multilingual support
   - Create content category classification

3. **Data Analysis Pipeline Enhancement**
   - Add alternative LLM providers and fallbacks
   - Implement result caching for performance
   - Create better result aggregation strategies

## Conclusion

The WebContentAnalyzer demonstrates a good data architecture for its domain-specific purpose. The application effectively implements data extraction, transformation, and analysis pipelines without relying on a traditional database. While the architecture is functional, there are significant opportunities for improvement, particularly in formal data modeling, validation, and persistence strategies.

The most pressing data architecture improvements include implementing formal data models with Pydantic, enhancing the data processing pipeline with clear transformation steps, and improving data persistence with a proper repository pattern. These enhancements would lead to better type safety, clearer data flows, and more maintainable code.

The application's strength lies in its content chunking and processing capabilities, which handle the challenges of large text analysis well. However, the lack of formal data models and validation makes the system more prone to errors and harder to maintain as it scales. Implementing the recommended improvements would significantly enhance the robustness and maintainability of the data architecture.
