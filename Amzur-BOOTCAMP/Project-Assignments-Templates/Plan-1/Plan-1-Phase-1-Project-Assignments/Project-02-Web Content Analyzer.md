# **Project 2: Web Content Analyzer**

## **Objective (Why?)**

Build a web application that extracts content from any website URL and generates a comprehensive analysis report using LLM in just 2 days. This accelerated timeline leverages your Project 1 experience for rapid development. You will practice:

* **Web Scraping**: Extracting content from websites using Python  
* **Content Processing**: Cleaning and structuring scraped data  
* **LLM Integration**: Using AI to analyze and summarize content  
* **API Development**: Building more complex backend endpoints

  ## **Core Requirements (Must-have)**

| Layer | Requirement |
| :---: | ----- |
| **Backend** | Python 3.11 \+ FastAPI POST /analyze endpoint: {"url": "https://example.com"} → {"analysis": "detailed report"} Web scraping with requests \+ BeautifulSoup Content extraction: title, headings, main text, links LLM integration for content analysis Error handling for invalid URLs and scraping failures |
| **Frontend** | Streamlit \- Perfect for data analysis and reporting Use st.text\_input() for URL input Use st.button() for analysis trigger Use st.expander() and st.columns() for report layout Use st.progress() for processing indicators |
| **Content  Processing** | Extract meaningful content (skip navigation, ads, etc.) Limit content size to prevent API limits Structure data for LLM analysis Handle different website layouts |

  ## 

  ## 

## **Development Approach: Milestone-Based Progression**

**Philosophy**: Focus on **deliverable quality** and **comprehensive review compliance** rather than rigid timelines. Each milestone must pass all relevant review templates from our Templates folder before proceeding.

### **Milestone 1: Web Scraping Foundation & Data Extraction**
**Estimated Time**: 4-8 hours (flexible based on learning pace)

#### **Deliverables:**
- [ ] Working development environment with web scraping dependencies
- [ ] BeautifulSoup-based content extraction service
- [ ] URL validation and error handling system
- [ ] Basic Streamlit interface for URL input and results display
- [ ] Content preprocessing and cleaning pipeline

#### **Review Requirements (Must Pass to Proceed):**
- [ ] **Security Review**: Input validation, URL sanitization, no SSRF vulnerabilities
- [ ] **Code Quality Review**: Clean separation of scraping logic and UI
- [ ] **Performance Review**: Efficient content extraction and memory management

### **Milestone 2: LLM Integration & Analysis Engine**
**Estimated Time**: 4-6 hours (flexible based on Milestone 1 completion)

#### **Deliverables:**
- [ ] LLM integration for content analysis and report generation
- [ ] Structured analysis pipeline with content preprocessing
- [ ] Comprehensive error handling for analysis failures
- [ ] Enhanced UI with progress indicators and analysis results
- [ ] Content size optimization and intelligent truncation

#### **Review Requirements (Must Pass to Proceed):**
- [ ] **AI Integration Review**: Optimal prompt engineering and content analysis
- [ ] **Performance Review**: Response times and content processing efficiency
- [ ] **Security Review**: Content sanitization and safe processing

### **Milestone 3: Production Features & Advanced Analysis**
**Estimated Time**: 3-5 hours (flexible based on previous milestones)

#### **Deliverables:**
- [ ] Advanced report formatting and data visualization
- [ ] Batch processing capabilities for multiple URLs
- [ ] Export functionality (PDF, JSON, CSV)
- [ ] Comprehensive documentation and testing procedures
- [ ] Production-ready deployment preparation

#### **Review Requirements (Must Pass for Project Completion):**
- [ ] **Architecture Review**: Scalable design and component separation
- [ ] **Security Review**: Complete security assessment including SSRF prevention
- [ ] **AI Integration Review**: Production-ready analysis pipeline
- [ ] **Code Quality Review**: Final code quality and documentation standards

### **Milestone Progression Rules:**
- **Cannot advance** to next milestone without passing all review requirements
- **Flexible timing** allows for learning at individual pace
- **Quality gates** ensure each milestone meets professional standards
- **Mentor support** available for concept clarification and review failures

  ## **Technical Specifications**

  ### **API Endpoint Structure**

* Build a POST /analyze endpoint that accepts URLs and returns structured analysis reports  
* Implement web scraping logic to extract meaningful content from various website layouts

  ### **Content Extraction Strategy**

* Use BeautifulSoup to target main content elements while filtering out navigation, ads, and boilerplate  
* Implement content size limits and intelligent truncation to handle large websites

  ### **LLM Analysis Integration**

* Design prompts that generate structured reports covering company overview, services, and key highlights  
* Handle API rate limits and content size restrictions for optimal LLM processing

  ## **Project Structure**

* Organize code with separate modules for Streamlit UI, web scraping, and LLM analysis  
* Use clean separation between data extraction, processing, and presentation layers

  ## 

  ## **Stretch Goals (Nice-to-have)**

* **Multiple URL Analysis**: Analyze and compare multiple websites  
* **Export Functionality**: Download analysis as PDF or Word document  
* **Analysis History**: Store and retrieve previous analyses  
* **Advanced Scraping**: Handle JavaScript-heavy sites with Selenium  
* **Content Categories**: Detect and categorize different types of websites

  ## **Deliverables**

1. **GitHub Repository Link** (public or invite @mentor)  
2. **Live Demo** with 3+ different website analyses  
3. **ANALYSIS\_SAMPLES.md** \- Include:  
   * Sample analyses of different website types (company, blog, news)  
   * Screenshots of the application  
   * List of tested URLs and results  
4. **Technical\_Learnings.md**

## **Measurable Goals & Review Template Compliance**

### **Primary Objectives (Must Complete for Project Advancement)**
- [ ] **Web Scraping Excellence**: 80%+ success rate on diverse website scraping
- [ ] **Security Compliance**: Pass Security Review with 9/10+ score (SSRF prevention critical)
- [ ] **AI Integration Quality**: Pass AI Integration Review with 8.5/10+ score
- [ ] **Performance Standards**: Process websites within 30 seconds, handle large content efficiently
- [ ] **Code Quality Standards**: Pass Code Quality Review with 8/10+ score

### **Review Template Integration (All Must Pass)**

#### **Security Review Requirements (Critical for Web Scraping)**
```python
security_criteria = {
    "ssrf_prevention": {"target": 100, "weight": 35},          # Server-Side Request Forgery prevention
    "input_validation": {"target": 95, "weight": 25},          # URL validation and sanitization
    "content_sanitization": {"target": 90, "weight": 20},      # Safe content processing
    "error_handling": {"target": 85, "weight": 15},            # Secure error messages
    "dependency_security": {"target": 90, "weight": 5}         # Secure web scraping libraries
}
```

#### **AI Integration Review Requirements**
```python
ai_integration_criteria = {
    "content_preprocessing": {"target": 90, "weight": 30},     # Content cleaning and optimization
    "prompt_engineering": {"target": 85, "weight": 25},        # Effective analysis prompts
    "analysis_quality": {"target": 85, "weight": 20},          # Meaningful insights generation
    "cost_efficiency": {"target": 80, "weight": 15},           # Token optimization
    "error_handling": {"target": 85, "weight": 10}             # AI service failure management
}
```

#### **Performance Review Requirements**
```python
performance_criteria = {
    "scraping_speed": {"target": 85, "weight": 30},            # Efficient content extraction
    "memory_management": {"target": 80, "weight": 25},         # Large content handling
    "response_time": {"target": 85, "weight": 20},             # End-to-end processing time
    "resource_optimization": {"target": 75, "weight": 15},     # CPU and memory efficiency
    "concurrent_handling": {"target": 70, "weight": 10}        # Multiple request support
}
```

### **Performance Standards**
- **Scraping Success Rate**: 80%+ on diverse websites (news, blogs, corporate sites)
- **Processing Time**: Average < 30 seconds for standard web pages
- **Content Quality**: Extract meaningful content while filtering noise
- **Memory Efficiency**: Handle large websites without memory issues
- **Error Rate**: < 10% failed scraping attempts due to code issues

## **Task Tracking & Project Management Integration**

### **Epic: Project 2 - Web Content Analyzer**
**Epic ID**: P2-ANALYZER  
**Priority**: High  
**Estimated Effort**: 11-19 hours  
**Assignee**: [Candidate Name]  
**Reviewer**: [Mentor Name]  
**Dependencies**: Project 1 completion

### **Milestone 1: Web Scraping Foundation & Data Extraction**

#### **Feature 2.1: Web Scraping Infrastructure**
**Task ID**: P2-M1-SCRAPING  
**Priority**: Critical  
**Estimated**: 3-4 hours  
**Dependencies**: None

**Sub-tasks:**
- [ ] **P2-M1-SCRAPING-01**: Setup BeautifulSoup scraping service
  - **Description**: Create web scraping service with proper error handling
  - **Acceptance Criteria**: 
    - BeautifulSoup integration working
    - URL validation implemented
    - Basic content extraction functional
  - **Estimated**: 90 minutes

- [ ] **P2-M1-SCRAPING-02**: Implement content cleaning pipeline
  - **Description**: Extract meaningful content while filtering noise
  - **Acceptance Criteria**:
    - Remove navigation, ads, boilerplate
    - Extract title, headings, main content
    - Handle different website layouts
  - **Estimated**: 90 minutes

- [ ] **P2-M1-SCRAPING-03**: Add security and validation
  - **Description**: Implement SSRF prevention and input validation
  - **Acceptance Criteria**:
    - URL whitelist/blacklist functionality
    - Private IP address blocking
    - Content size limits
  - **Estimated**: 60 minutes

#### **Feature 2.2: Basic Analysis Interface**
**Task ID**: P2-M1-UI  
**Priority**: High  
**Estimated**: 2-3 hours  
**Dependencies**: P2-M1-SCRAPING

**Sub-tasks:**
- [ ] **P2-M1-UI-01**: Create URL input interface
  - **Description**: Streamlit interface for URL input and basic results
  - **Acceptance Criteria**:
    - URL input field with validation
    - Progress indicators during scraping
    - Basic results display
  - **Estimated**: 75 minutes

- [ ] **P2-M1-UI-02**: Implement error handling UI
  - **Description**: User-friendly error messages and recovery
  - **Acceptance Criteria**:
    - Clear error messages for failed scrapes
    - Retry mechanisms
    - Input validation feedback
  - **Estimated**: 45 minutes

### **Review Tasks for Milestone 1**
**Task ID**: P2-R1-REVIEW  
**Priority**: Blocking  
**Estimated**: 2.5-3 hours  
**Dependencies**: P2-M1-UI  
**Assignee**: [Mentor Name]

**Sub-tasks:**
- [ ] **P2-R1-REVIEW-01**: Security Review (Milestone 1)
  - **Focus**: SSRF prevention, input validation, secure scraping
  - **Pass Criteria**: Score ≥ 9.0/10
  - **Estimated**: 60 minutes

- [ ] **P2-R1-REVIEW-02**: Performance Review (Milestone 1)
  - **Focus**: Scraping efficiency, memory management
  - **Pass Criteria**: Score ≥ 8.0/10
  - **Estimated**: 45 minutes

- [ ] **P2-R1-REVIEW-03**: Code Quality Review (Milestone 1)
  - **Focus**: Clean architecture, separation of concerns
  - **Pass Criteria**: Score ≥ 7.5/10
  - **Estimated**: 45 minutes

### **Phase Progression Requirements**
#### **Project 2 → Project 3 Advancement Requirements**

**Mandatory Review Template Compliance:**
- [ ] **Security Review**: Minimum 9.0/10 (SSRF prevention critical)
- [ ] **AI Integration Review**: Minimum 8.5/10 (content analysis quality)
- [ ] **Performance Review**: Minimum 8.0/10 (efficient processing)
- [ ] **Code Quality Review**: Minimum 8.0/10 (clean architecture)

**Technical Competency Validation:**
```python
advancement_requirements = {
    "review_compliance": {
        "security": {"minimum_score": 9.0, "weight": 35},      # Critical for web scraping
        "ai_integration": {"minimum_score": 8.5, "weight": 30}, # Content analysis quality
        "performance": {"minimum_score": 8.0, "weight": 20},   # Processing efficiency
        "code_quality": {"minimum_score": 8.0, "weight": 15}   # Architecture quality
    },
    "functional_requirements": {
        "scraping_success_rate": 80,
        "processing_time_seconds": 30,
        "diverse_website_support": True
    }
}
```

### **Project 3 Preparation Requirements**
- [ ] **Data Storage Basics**: Learn JSON/SQLite for knowledge repositories
- [ ] **Search Algorithms**: Understand text search and matching
- [ ] **Scope Management**: Learn to build domain-specific AI systems
- [ ] **Profile Data Extraction**: Advanced scraping for structured data

---

   ## **Evaluation Rubric (100 Points)**

| Criterion | Points | Details |
| :---: | ----- | ----- |
| **Web Scraping** | 30 pts | • Successfully extracts content from various sites • Handles different website structures • Proper error handling for failed scrapes |
| **LLM Analysis** | 25 pts | • Generates meaningful, structured analysis • Good prompt engineering • Handles content size limits |
| **API Design** | 20 pts | • Clean endpoint structure • Proper request/response format • Error handling and validation |
| **Frontend** | 15 pts | • User-friendly interface • Good UX during processing • Clear display of results |
| **Code Quality** | 10 pts | • Clean, documented code • Proper project structure • Good README |

   ## 

   ## 

   ## **Testing Websites**

Test your analyzer with these different types of websites:

* **Corporate**: [https://www.amzur.com](https://www.amzur.com), [https://www.microsoft.com](https://www.microsoft.com) , [https://www.apple.com](https://www.apple.com)   
* **E-commerce**: [https://www.amazon.com](https://www.amazon.com) , [https://www.shopify.com](https://www.shopify.com)   
* **News**: [https://www.bbc.com](https://www.bbc.com) , [https://techcrunch.com](https://techcrunch.com)   
* **Blog**: [https://medium.com](https://medium.com) , [https://dev.to](https://dev.to)   
* **Educational**: [https://www.coursera.org](https://www.coursera.org) , [https://www.edx.org](https://www.edx.org) 

  ## **Quick Start Resources**

* **BeautifulSoup Documentation**: [https://www.crummy.com/software/BeautifulSoup/bs4/doc/](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)  
* **Requests Library**: [https://docs.python-requests.org/](https://docs.python-requests.org/)  
* **Web Scraping Guide**: [https://realpython.com/beautiful-soup-web-scraper-python/](https://realpython.com/beautiful-soup-web-scraper-python/)  
* **FastAPI Background Tasks**: [https://fastapi.tiangolo.com/tutorial/background-tasks/](https://fastapi.tiangolo.com/tutorial/background-tasks/)

  ## **FAQ**

* **"What if a website blocks scraping?"** Handle gracefully with error messages and try different user agents  
* **"Should I handle all website types?"** Focus on standard HTML sites; advanced JavaScript rendering is stretch goal  
* **"How detailed should the analysis be?"** Aim for 200-500 words, structured and informative  
* **"Can I use Selenium?"** For basic requirements, use requests \+ BeautifulSoup. Selenium is a stretch goal

  ## **Success Criteria Checklist**

* Can successfully scrape content from 80%+ of tested websites  
* Generates meaningful analysis reports using LLM  
* Handles errors gracefully (network issues, invalid URLs)  
* User-friendly interface with loading states  
* Clean, documented code with proper structure  
* Comprehensive testing with various website types

