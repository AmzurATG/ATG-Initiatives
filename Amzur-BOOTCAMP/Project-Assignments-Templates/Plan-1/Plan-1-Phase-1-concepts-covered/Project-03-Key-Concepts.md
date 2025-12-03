# Project 3: Smart Knowledge Repository - Key Concepts

## 1. Structured Data Extraction

### Concept Overview
Structured data extraction involves systematically scraping specific information from websites and organizing it into standardized formats suitable for storage and analysis.

### Problem It Solves
While general web scraping retrieves all content, applications often need only specific structured information (like people profiles, product details, or financial data) in consistent formats for analysis and display.

### Solution Approach
- **Targeted Extraction**: Focusing on specific data points rather than entire pages
- **Schema Definition**: Creating clear data models for extracted information
- **Pattern Matching**: Using consistent selectors and patterns to extract similar data across pages
- **Data Validation**: Ensuring extracted data conforms to expected formats and constraints

### Implementation Insight
```python
from dataclasses import dataclass
from typing import Optional, List
from bs4 import BeautifulSoup
import requests

@dataclass
class PersonProfile:
    """Data model for a leadership profile"""
    name: str
    role: str
    department: str
    bio: str
    photo_url: Optional[str] = None
    email: Optional[str] = None
    linkedin: Optional[str] = None
    twitter: Optional[str] = None

class LeadershipProfileScraper:
    """Scraper for leadership team profiles"""
    
    def __init__(self, base_url: str):
        self.base_url = base_url
        self.session = requests.Session()
        self.session.headers.update({
            "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36"
        })
        
    def scrape_leadership_team(self) -> List[PersonProfile]:
        """Scrape all leadership profiles from the team page"""
        profiles = []
        
        # Get the main leadership team page
        team_page = self._get_page(f"{self.base_url}/about/leadership")
        if not team_page:
            return profiles
            
        # Find all profile links
        profile_links = []
        for link in team_page.select("a.team-member-card"):
            href = link.get("href")
            if href:
                profile_links.append(href)
                
        # Scrape individual profile pages
        for link in profile_links:
            profile = self._scrape_profile_page(link)
            if profile:
                profiles.append(profile)
                
        return profiles
        
    def _get_page(self, url: str) -> Optional[BeautifulSoup]:
        """Get and parse a page"""
        try:
            response = self.session.get(url, timeout=10)
            response.raise_for_status()
            return BeautifulSoup(response.text, "html.parser")
        except Exception as e:
            logger.error(f"Failed to get page {url}: {str(e)}")
            return None
            
    def _scrape_profile_page(self, url: str) -> Optional[PersonProfile]:
        """Extract structured data from a profile page"""
        soup = self._get_page(url)
        if not soup:
            return None
            
        try:
            # Extract structured data using consistent selectors
            name = soup.select_one("h1.profile-name").text.strip()
            role = soup.select_one("h2.profile-role").text.strip()
            department = soup.select_one("div.profile-department").text.strip()
            bio = soup.select_one("div.profile-bio").text.strip()
            
            # Extract optional fields
            photo_el = soup.select_one("img.profile-photo")
            photo_url = photo_el.get("src") if photo_el else None
            
            email_el = soup.select_one("a.profile-email")
            email = email_el.text.strip() if email_el else None
            
            # Create structured profile
            return PersonProfile(
                name=name,
                role=role,
                department=department,
                bio=bio,
                photo_url=photo_url,
                email=email
            )
        except Exception as e:
            logger.error(f"Failed to parse profile {url}: {str(e)}")
            return None
```

### Common Pitfalls
- **Brittle Selectors**: Using CSS selectors that break when website structure changes
- **Missing Error Handling**: Not accounting for missing or malformed data in source websites
- **Rate Limiting Issues**: Sending too many requests too quickly, triggering IP blocks
- **Incomplete Schema Design**: Creating data models that don't capture all necessary information
- **Validation Gaps**: Not properly validating extracted data against expected formats
- **Ethics and Legal Issues**: Not respecting robots.txt, terms of service, or copyright restrictions

### Cross-References
- Builds on [Project 2's Web Scraping](./Project-2-Key-Concepts.md#1-web-scraping-and-data-extraction) with more structured approach
- Feeds into [Knowledge Base Design and Management](#2-knowledge-base-design-and-management) for storage
- Enables [Context-Aware AI Responses](#3-context-aware-ai-responses) by providing structured data
- Requires [Project 1's Comprehensive Error Handling](./Project-1-Key-Concepts.md#5-comprehensive-error-handling)

### Architecture Diagram
```
┌───────────────┐     ┌───────────────────────┐     ┌───────────────┐
│               │     │                       │     │               │
│  Web Source   │────►│  Structured Extractor │────►│  Data Models  │
│               │     │                       │     │               │
└───────────────┘     └───────────────────────┘     └───────┬───────┘
                               │                            │
                               │                            │
                               ▼                            ▼
                      ┌────────────────┐          ┌─────────────────┐
                      │                │          │                 │
                      │   Validation   │          │  Knowledge Base │
                      │                │          │                 │
                      └────────────────┘          └─────────────────┘
```

### Further Resources
- [Beautiful Soup Documentation](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
- [Scrapy Framework](https://scrapy.org/) for larger scraping projects
- [Web Scraping Ethics and Legal Guidelines](https://www.scrapehero.com/how-to-prevent-getting-blacklisted-while-scraping/)
- [Python Data Classes Documentation](https://docs.python.org/3/library/dataclasses.html)
- [Schema.org](https://schema.org/) for standardized data structure definitions

## 2. Knowledge Base Design and Management

### Concept Overview
Knowledge base design involves creating structured storage systems for domain-specific information with efficient search and retrieval capabilities.

### Problem It Solves
Applications need to store, organize, and quickly retrieve information for user queries without complex database infrastructure, especially when operating on limited knowledge domains.

### Solution Approach
- **Simple Storage**: Using file-based storage (JSON) or lightweight databases (SQLite)
- **Search Indexing**: Creating text indices for efficient retrieval
- **Categorization**: Organizing information by meaningful attributes
- **Metadata Enhancement**: Adding additional contextual information to improve search

### Implementation Insight
```python
import json
import os
from typing import List, Dict, Any, Optional
from dataclasses import asdict
import sqlite3
from contextlib import contextmanager

class KnowledgeRepository:
    """Simple knowledge repository for storing and retrieving profile data"""
    
    def __init__(self, db_path: str = "knowledge_base.db"):
        self.db_path = db_path
        self._initialize_db()
    
    def _initialize_db(self):
        """Create database schema if not exists"""
        with self._get_connection() as conn:
            cursor = conn.cursor()
            
            # Create profiles table
            cursor.execute('''
                CREATE TABLE IF NOT EXISTS profiles (
                    id INTEGER PRIMARY KEY AUTOINCREMENT,
                    name TEXT NOT NULL,
                    role TEXT NOT NULL,
                    department TEXT NOT NULL,
                    bio TEXT NOT NULL,
                    photo_url TEXT,
                    email TEXT,
                    linkedin TEXT,
                    twitter TEXT,
                    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
                )
            ''')
            
            # Create search index
            cursor.execute('''
                CREATE VIRTUAL TABLE IF NOT EXISTS profile_search 
                USING FTS5(name, role, department, bio)
            ''')
            
            conn.commit()
    
    @contextmanager
    def _get_connection(self):
        """Context manager for database connections"""
        conn = sqlite3.connect(self.db_path)
        try:
            yield conn
        finally:
            conn.close()
    
    def store_profile(self, profile: PersonProfile) -> int:
        """Store a profile in the knowledge base"""
        with self._get_connection() as conn:
            cursor = conn.cursor()
            
            # Convert dataclass to dict
            profile_dict = asdict(profile)
            
            # Insert into profiles table
            columns = ', '.join(profile_dict.keys())
            placeholders = ', '.join(['?'] * len(profile_dict))
            values = tuple(profile_dict.values())
            
            cursor.execute(
                f"INSERT INTO profiles ({columns}) VALUES ({placeholders})",
                values
            )
            
            # Get the inserted ID
            profile_id = cursor.lastrowid
            
            # Update search index
            cursor.execute(
                "INSERT INTO profile_search (rowid, name, role, department, bio) VALUES (?, ?, ?, ?, ?)",
                (profile_id, profile.name, profile.role, profile.department, profile.bio)
            )
            
            conn.commit()
            return profile_id
    
    def search_profiles(self, query: str) -> List[Dict[str, Any]]:
        """Search profiles by text query"""
        with self._get_connection() as conn:
            conn.row_factory = sqlite3.Row
            cursor = conn.cursor()
            
            # Search using FTS5 virtual table
            cursor.execute(
                "SELECT p.* FROM profiles p JOIN profile_search ps ON p.id = ps.rowid " +
                "WHERE profile_search MATCH ? ORDER BY rank",
                (query,)
            )
            
            return [dict(row) for row in cursor.fetchall()]
    
    def get_profiles_by_department(self, department: str) -> List[Dict[str, Any]]:
        """Get profiles filtered by department"""
        with self._get_connection() as conn:
            conn.row_factory = sqlite3.Row
            cursor = conn.cursor()
            
            cursor.execute(
                "SELECT * FROM profiles WHERE department = ? ORDER BY name",
                (department,)
            )
            
            return [dict(row) for row in cursor.fetchall()]
            
    def get_all_departments(self) -> List[str]:
        """Get list of all departments"""
        with self._get_connection() as conn:
            cursor = conn.cursor()
            cursor.execute("SELECT DISTINCT department FROM profiles ORDER BY department")
            return [row[0] for row in cursor.fetchall()]
```

### Common Pitfalls
- **Schema Rigidity**: Creating overly rigid schemas that can't evolve with changing requirements
- **Missing Indexes**: Not properly indexing fields used for frequent searches, causing performance issues
- **Connection Management**: Not properly closing database connections, leading to resource leaks
- **Over-normalization**: Creating too many related tables for a simple knowledge base, adding complexity
- **Inadequate Backup**: Not implementing proper backup strategies for knowledge base content
- **Text Search Limitations**: Not understanding the limitations of full-text search implementations

### Cross-References
- Takes input from [Structured Data Extraction](#1-structured-data-extraction) for content
- Provides foundation for [Context-Aware AI Responses](#3-context-aware-ai-responses)
- Enables [Data Categorization and Organization](#4-data-categorization-and-organization) for browsing
- Uses concepts similar to [Project 4's Database Design](./Project-4-Key-Concepts.md) but with simpler implementation

### Architecture Diagram
```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│                  │     │                  │     │                  │
│  Input Sources   │────►│ Data Processing  │────►│  Storage Layer   │
│  (Web Scraping)  │     │ & Validation     │     │  (SQLite/JSON)   │
│                  │     │                  │     │                  │
└──────────────────┘     └──────────────────┘     └────────┬─────────┘
                                                           │
                                                           │
             ┌───────────────────────────────────┬─────────┴─────────┐
             │                                   │                   │
             ▼                                   ▼                   ▼
    ┌─────────────────┐               ┌──────────────────┐  ┌─────────────────┐
    │                 │               │                  │  │                 │
    │  Search Index   │               │  Category Index  │  │  Direct Access  │
    │  (FTS5)         │               │  (Department)    │  │  (By ID)        │
    │                 │               │                  │  │                 │
    └─────────────────┘               └──────────────────┘  └─────────────────┘
```

### Further Resources
- [SQLite Documentation](https://www.sqlite.org/docs.html)
- [SQLite FTS5 Extension](https://www.sqlite.org/fts5.html) for full-text search
- [Knowledge Base Design Patterns](https://martinfowler.com/articles/patterns-of-distributed-systems/)
- [Database Connection Pooling](https://docs.python.org/3/library/sqlite3.html#connection-objects)
- [JSON Schema](https://json-schema.org/) for document-based knowledge bases

## 3. Context-Aware AI Responses

### Concept Overview
Context-aware AI responses involve generating answers that are informed by and limited to a specific knowledge domain, creating more accurate and relevant responses for domain-specific applications.

### Problem It Solves
General-purpose AI models often provide generic responses that may be irrelevant, include hallucinated information, or go beyond the specific knowledge domain of an application.

### Solution Approach
- **Knowledge Grounding**: Limiting AI responses to information contained in the knowledge base
- **Relevance Matching**: Finding the most relevant knowledge for a given query
- **Response Construction**: Formulating natural language responses based on retrieved knowledge
- **Scope Detection**: Determining if a query is answerable within the available knowledge

### Implementation Insight
```python
from typing import List, Dict, Any, Optional, Tuple

class ContextAwareAI:
    """AI service that answers questions based on knowledge repository data"""
    
    def __init__(self, knowledge_repo: KnowledgeRepository, llm_service):
        self.knowledge_repo = knowledge_repo
        self.llm_service = llm_service
    
    async def answer_question(self, question: str) -> Dict[str, Any]:
        """Generate a context-aware answer to user question"""
        # Step 1: Determine if the question is within scope
        is_in_scope, scope_category = self._check_question_scope(question)
        
        if not is_in_scope:
            return {
                "answer": self._generate_out_of_scope_response(question, scope_category),
                "in_scope": False,
                "relevant_profiles": [],
                "confidence": 0.0
            }
        
        # Step 2: Find relevant knowledge
        relevant_profiles = self._retrieve_relevant_knowledge(question)
        
        if not relevant_profiles:
            return {
                "answer": "I don't have enough information to answer that question confidently.",
                "in_scope": True,
                "relevant_profiles": [],
                "confidence": 0.1
            }
        
        # Step 3: Generate answer based on retrieved knowledge
        answer, confidence = await self._generate_grounded_answer(
            question, relevant_profiles
        )
        
        return {
            "answer": answer,
            "in_scope": True,
            "relevant_profiles": [p["id"] for p in relevant_profiles],
            "confidence": confidence
        }
    
    def _check_question_scope(self, question: str) -> Tuple[bool, str]:
        """Determine if question is within the knowledge domain scope"""
        # Check for leadership/people related keywords
        leadership_keywords = [
            "who", "person", "leader", "executive", "team", 
            "role", "ceo", "cto", "coo", "background", "bio"
        ]
        
        # Check if any leadership keywords are in the question
        if any(keyword in question.lower() for keyword in leadership_keywords):
            return True, "leadership"
            
        return False, "unknown"
    
    def _retrieve_relevant_knowledge(self, question: str) -> List[Dict[str, Any]]:
        """Find relevant profiles for the question"""
        # Extract key search terms from question
        # (In a real implementation, this would use NLP techniques)
        search_terms = question.lower()
        for term in ["who", "is", "the", "what", "where", "when", "how", "?", "."]:
            search_terms = search_terms.replace(term, " ")
        
        # Search knowledge base with extracted terms
        profiles = self.knowledge_repo.search_profiles(search_terms)
        
        # Sort by relevance (if your search doesn't already do this)
        # In a real implementation, this would use more sophisticated ranking
        return profiles[:3]  # Return top 3 most relevant profiles
    
    async def _generate_grounded_answer(
        self, question: str, profiles: List[Dict[str, Any]]
    ) -> Tuple[str, float]:
        """Generate an answer grounded in the retrieved profiles"""
        # Prepare context from profiles
        context = "\n\n".join([
            f"Name: {p['name']}\nRole: {p['role']}\nDepartment: {p['department']}\nBio: {p['bio']}"
            for p in profiles
        ])
        
        # Create prompt for LLM
        prompt = f"""
        Answer the following question using ONLY the information provided in the context below. 
        If the answer cannot be determined from the context, say "I don't have enough information to answer that confidently."
        
        Context:
        {context}
        
        Question: {question}
        
        Answer:
        """
        
        # Generate answer using LLM
        try:
            response = await self.llm_service.generate_text(prompt, max_tokens=300)
            # In a real system, we'd calculate confidence based on multiple factors
            confidence = 0.85  # Simplified confidence score
            return response, confidence
        except Exception as e:
            logger.error(f"Failed to generate answer: {str(e)}")
            return "I'm having trouble generating an answer right now.", 0.0
    
    def _generate_out_of_scope_response(self, question: str, scope_category: str) -> str:
        """Generate helpful response for out-of-scope questions"""
        if scope_category == "unknown":
            return (
                "I'm specifically trained to answer questions about the leadership team. "
                "I don't have information about other topics. Would you like to know "
                "about any of our executives or their roles?"
            )
        
        # For other specific out-of-scope categories, customize responses
        return (
            "That's outside the scope of my knowledge. I can help with questions "
            "about our leadership team, such as their roles, departments, or backgrounds."
        )
```

## 4. Data Categorization and Organization

### Concept Overview
Data categorization and organization involves structuring information into logical groups and hierarchies to facilitate navigation, filtering, and retrieval.

### Problem It Solves
Raw data storage makes it difficult for users to browse, explore, and find relevant information without knowing exactly what they're looking for, especially in large datasets.

### Solution Approach
- **Hierarchical Organization**: Arranging data in meaningful hierarchies (e.g., by department)
- **Faceted Classification**: Creating multiple ways to filter and view the same data
- **Tag-Based Systems**: Using tags or attributes for flexible, multi-dimensional organization
- **User-Friendly Navigation**: Building interfaces that reveal organizational structure

### Implementation Insight
```python
import streamlit as st
import pandas as pd
from typing import List, Dict, Any

def display_organized_profiles(profiles: List[Dict[str, Any]], departments: List[str]):
    """Display profiles organized by department with filtering options"""
    st.header("Leadership Profiles")
    
    # Create sidebar filters
    st.sidebar.header("Filters")
    
    # Department filter
    selected_dept = st.sidebar.selectbox(
        "Department", 
        options=["All Departments"] + departments
    )
    
    # Role level filter
    role_levels = ["All Levels", "Executive", "Director", "Manager"]
    selected_role = st.sidebar.selectbox("Role Level", options=role_levels)
    
    # Apply filters
    filtered_profiles = profiles
    if selected_dept != "All Departments":
        filtered_profiles = [p for p in filtered_profiles if p["department"] == selected_dept]
        
    if selected_role != "All Levels":
        filtered_profiles = [p for p in filtered_profiles if selected_role.lower() in p["role"].lower()]
    
    # Show filter results summary
    st.write(f"Showing {len(filtered_profiles)} of {len(profiles)} profiles")
    
    # Display as tabs by department
    if selected_dept == "All Departments":
        tabs = st.tabs(departments)
        
        for i, dept in enumerate(departments):
            with tabs[i]:
                dept_profiles = [p for p in filtered_profiles if p["department"] == dept]
                display_profile_cards(dept_profiles)
    else:
        # When a specific department is selected, show without tabs
        display_profile_cards(filtered_profiles)

def display_profile_cards(profiles: List[Dict[str, Any]]):
    """Display profiles as visual cards in a grid"""
    if not profiles:
        st.info("No profiles match the selected filters.")
        return
        
    # Use columns to create a grid layout
    cols_per_row = 3
    for i in range(0, len(profiles), cols_per_row):
        cols = st.columns(cols_per_row)
        for j in range(cols_per_row):
            if i + j < len(profiles):
                with cols[j]:
                    profile = profiles[i + j]
                    with st.container():
                        if profile.get("photo_url"):
                            st.image(profile["photo_url"], width=150)
                        st.subheader(profile["name"])
                        st.write(f"**{profile['role']}**")
                        st.write(f"_{profile['department']}_")
                        
                        with st.expander("View Bio"):
                            st.write(profile["bio"])
                            
                            if profile.get("email"):
                                st.write(f"📧 {profile['email']}")
                            
                            if profile.get("linkedin"):
                                st.write(f"[LinkedIn Profile]({profile['linkedin']})")
```

## 5. Intelligent Query Processing

### Concept Overview
Intelligent query processing involves analyzing user questions to understand intent, extract key information, and route to appropriate knowledge resources.

### Problem It Solves
Natural language questions are ambiguous and may not match the exact structure or keywords of stored information, making direct text matching insufficient for accurate information retrieval.

### Solution Approach
- **Query Understanding**: Analyzing user questions for intent and key entities
- **Query Expansion**: Adding related terms and synonyms to improve matching
- **Semantic Search**: Using embeddings or semantic understanding rather than exact keywords
- **Relevance Ranking**: Scoring and ordering results by likelihood of matching user intent

### Implementation Insight
```python
from typing import List, Dict, Any, Tuple
import re
import nltk
from nltk.corpus import stopwords
from nltk.stem import WordNetLemmatizer
from sentence_transformers import SentenceTransformer
import numpy as np

class IntelligentQueryProcessor:
    """Process natural language queries for intelligent information retrieval"""
    
    def __init__(self):
        # Download necessary NLTK data (in production, this would be done during setup)
        try:
            nltk.data.find('corpora/stopwords')
        except LookupError:
            nltk.download('stopwords')
            nltk.download('wordnet')
        
        self.stop_words = set(stopwords.words('english'))
        self.lemmatizer = WordNetLemmatizer()
        
        # Initialize sentence transformer model for semantic search
        self.model = SentenceTransformer('all-MiniLM-L6-v2')  # Small, efficient model
        
        # Common variations of leadership terms
        self.role_synonyms = {
            "ceo": ["chief executive officer", "chief exec", "company head"],
            "cto": ["chief technology officer", "tech head", "technology chief"],
            "cfo": ["chief financial officer", "finance head", "financial director"],
            "coo": ["chief operating officer", "operations head", "operations director"],
            "vp": ["vice president", "vice-president"],
            "svp": ["senior vice president", "senior vp"],
            "evp": ["executive vice president", "executive vp"],
        }
    
    def preprocess_query(self, query: str) -> str:
        """Clean and normalize the query text"""
        # Convert to lowercase
        text = query.lower()
        
        # Remove special characters and extra whitespace
        text = re.sub(r'[^\w\s]', ' ', text)
        text = re.sub(r'\s+', ' ', text).strip()
        
        return text
    
    def extract_keywords(self, query: str) -> List[str]:
        """Extract meaningful keywords from the query"""
        processed_query = self.preprocess_query(query)
        
        # Tokenize
        tokens = processed_query.split()
        
        # Remove stopwords and lemmatize
        keywords = [
            self.lemmatizer.lemmatize(token) 
            for token in tokens 
            if token not in self.stop_words
        ]
        
        # Expand role acronyms/synonyms
        expanded_keywords = []
        for keyword in keywords:
            expanded_keywords.append(keyword)
            
            # Add synonyms for common role acronyms
            if keyword in self.role_synonyms:
                expanded_keywords.extend(self.role_synonyms[keyword])
        
        return expanded_keywords
    
    def semantic_search(
        self, query: str, profiles: List[Dict[str, Any]], top_k: int = 3
    ) -> List[Dict[str, Any]]:
        """Perform semantic search to find relevant profiles"""
        # Create query embedding
        query_embedding = self.model.encode(query, convert_to_tensor=True)
        
        # Create embeddings for each profile (combine name, role, department, and bio)
        profile_texts = [
            f"{p['name']} {p['role']} {p['department']} {p['bio']}" 
            for p in profiles
        ]
        profile_embeddings = self.model.encode(profile_texts, convert_to_tensor=True)
        
        # Calculate similarity scores
        similarities = []
        for i, profile_embedding in enumerate(profile_embeddings):
            similarity = np.dot(query_embedding, profile_embedding) / (
                np.linalg.norm(query_embedding) * np.linalg.norm(profile_embedding)
            )
            similarities.append((similarity, i))
        
        # Sort by similarity score
        similarities.sort(reverse=True)
        
        # Return top_k most similar profiles
        return [
            {**profiles[idx], "relevance_score": float(score)} 
            for score, idx in similarities[:top_k]
        ]
    
    def process_query(
        self, query: str, profiles: List[Dict[str, Any]]
    ) -> Tuple[List[Dict[str, Any]], bool]:
        """Process a natural language query and return relevant profiles"""
        # Extract query intent and type
        query_type = self.classify_query_type(query)
        
        # Check if query is answerable from our knowledge base
        if query_type == "out_of_scope":
            return [], False
            
        # Find relevant profiles using semantic search
        relevant_profiles = self.semantic_search(query, profiles)
        
        return relevant_profiles, True
    
    def classify_query_type(self, query: str) -> str:
        """Determine the type and intent of a query"""
        query_lower = query.lower()
        
        # Check for leadership/people related keywords
        leadership_keywords = [
            "who", "person", "leader", "executive", "team", 
            "ceo", "cto", "cfo", "coo", "vp", "director",
            "head", "manager", "president", "chief"
        ]
        
        if any(keyword in query_lower for keyword in leadership_keywords):
            return "person_query"
            
        # Check for role/responsibility keywords
        role_keywords = ["role", "job", "position", "responsibility", "do", "responsible"]
        
        if any(keyword in query_lower for keyword in role_keywords):
            return "role_query"
            
        # Check for department keywords
        dept_keywords = ["department", "team", "group", "division", "work"]
        
        if any(keyword in query_lower for keyword in dept_keywords):
            return "department_query"
            
        # If no specific keywords match, check for general semantic similarity
        # to leadership-related concepts (would use embeddings in a real system)
        
        # Default to out of scope if we can't clearly identify the intent
        return "out_of_scope"
```

### Cross-References
- Links to [Knowledge Base Design and Management](#2-knowledge-base-design-and-management) for the storage architecture
- Serves as input to [Retrieval-Augmented Generation](#7-retrieval-augmented-generation) for context-aware responses
- Benefits from [Data Categorization and Organization](#4-data-categorization-and-organization) for better search results

### Visual Diagram
```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   User Query    │────►│ Query Analysis  │────►│ Entity & Intent │
└─────────────────┘     └─────────────────┘     └─────────────────┘
                                                        │
                                                        ▼
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Result Ranking │◄────│ Vector Matching │◄────│  Query Expansion│
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

### Common Pitfalls
- **Over-reliance on Keywords**: Focusing too much on keyword matching rather than semantic understanding
- **Missing Context**: Not considering the conversational context when processing queries
- **Poor Ranking**: Returning results based on simple similarity without considering relevance to intent
- **Ignoring Ambiguity**: Not handling cases where multiple interpretations of a query are possible

### Additional Resources
- [Sentence-Transformers Documentation](https://www.sbert.net/)
- [NLTK Tutorial for Text Processing](https://www.nltk.org/book/)
- [Vector Search Best Practices](https://www.pinecone.io/learn/vector-search-basics/)
- [Building Semantic Search from Scratch](https://towardsdatascience.com/building-a-semantic-search-engine-from-scratch-7a7db45fd748)

## 7. Retrieval-Augmented Generation (RAG)

### Concept Overview
Retrieval-Augmented Generation (RAG) combines information retrieval with generative AI to produce responses that are both contextually relevant and factually grounded in specific knowledge sources.

### Problem It Solves
Large language models often hallucinate or provide generic information when domain-specific accuracy is required, while lacking the ability to reference the most up-to-date or specialized information not in their training data.

### Solution Approach
- **Knowledge Retrieval**: Fetching relevant information from structured repositories based on the query
- **Context Augmentation**: Incorporating retrieved information into prompts for more informed responses
- **Source Attribution**: Providing citations and references to knowledge sources
- **Response Generation**: Using LLMs to synthesize natural language responses from the enhanced context

### Implementation Insight
```python
from typing import List, Dict, Any
import numpy as np
from sentence_transformers import SentenceTransformer
import openai

class RAGSystem:
    def __init__(self, knowledge_base, api_key):
        """Initialize the RAG system with a knowledge base and API key"""
        self.knowledge_base = knowledge_base
        openai.api_key = api_key
        
        # Initialize the embedding model
        self.embedding_model = SentenceTransformer('all-MiniLM-L6-v2')
        
    def retrieve_relevant_content(self, query: str, top_k: int = 3) -> List[Dict[str, Any]]:
        """Retrieve relevant documents/passages from the knowledge base"""
        # Encode the query
        query_embedding = self.embedding_model.encode(query)
        
        # Search for similar content in the knowledge base
        results = self.knowledge_base.search_by_vector(
            query_embedding, 
            top_k=top_k
        )
        
        return results
    
    def construct_augmented_prompt(self, query: str, retrieved_content: List[Dict[str, Any]]) -> str:
        """Construct a prompt that includes the retrieved information"""
        prompt = f"Question: {query}\n\nRelevant information:\n"
        
        for i, content in enumerate(retrieved_content):
            prompt += f"\n[{i+1}] {content['text']}"
            if 'source' in content:
                prompt += f" (Source: {content['source']})"
            prompt += "\n"
            
        prompt += "\nBased on the information above, please provide a comprehensive answer to the question. Include citations to the sources [1], [2], etc. where appropriate."
        
        return prompt
    
    def generate_response(self, prompt: str) -> str:
        """Generate a response using the LLM with the augmented prompt"""
        response = openai.ChatCompletion.create(
            model="gpt-3.5-turbo",
            messages=[
                {"role": "system", "content": "You are a helpful assistant that provides accurate information based on the sources given to you. Always cite your sources using the format [1], [2], etc."},
                {"role": "user", "content": prompt}
            ],
            temperature=0.3,  # Lower temperature for more factual responses
            max_tokens=500
        )
        
        return response.choices[0].message['content']
    
    def process_query(self, query: str) -> Dict[str, Any]:
        """Process a query through the RAG pipeline"""
        # Retrieve relevant content
        retrieved_content = self.retrieve_relevant_content(query)
        
        # If no relevant content was found
        if not retrieved_content:
            return {
                "answer": "I don't have specific information to answer this question accurately.",
                "sources": [],
                "has_relevant_info": False
            }
        
        # Construct augmented prompt
        prompt = self.construct_augmented_prompt(query, retrieved_content)
        
        # Generate response
        response = self.generate_response(prompt)
        
        # Return the response with metadata
        return {
            "answer": response,
            "sources": [item.get('source', 'Unknown') for item in retrieved_content],
            "has_relevant_info": True,
            "retrieved_content_count": len(retrieved_content)
        }
```

### Cross-References
- Builds on [Knowledge Base Design and Management](#2-knowledge-base-design-and-management) for information storage
- Leverages [Intelligent Query Processing](#5-intelligent-query-processing) for search optimization
- Enhances [Context-Aware AI Responses](#3-context-aware-ai-responses) with factual grounding

### Visual Diagram
```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   User Query    │────►│  Query Analysis │────►│ Vector Retrieval│
└─────────────────┘     └─────────────────┘     └─────────────────┘
                                                        │
                                                        ▼
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Final Response │◄────│  LLM Generation │◄────│ Context Assembly│
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

### Common Pitfalls
- **Retrieval-Generation Mismatch**: When retrieved information doesn't match what the LLM needs to answer the question
- **Prompt Overflow**: Including too much retrieved information, exceeding token limits
- **Citation Hallucination**: LLM referencing non-existent sources or sections
- **Irrelevant Retrieval**: Retrieving information that seems semantically similar but is actually irrelevant to the query intent
- **Conflicting Information**: Not handling contradictions between different retrieved sources

### Additional Resources
- [OpenAI RAG Best Practices](https://platform.openai.com/docs/guides/prompt-engineering/retrieval-augmented-generation)
- [LangChain RAG Documentation](https://python.langchain.com/docs/modules/data_connection/)
- [Pinecone Vector Database for RAG](https://www.pinecone.io/learn/retrieval-augmented-generation/)
- [Microsoft's RAGTruth Benchmark](https://github.com/microsoft/rag-truth)
- [Google's REALM Paper](https://arxiv.org/abs/2002.08909)

## 8. Scope-Aware Conversational Interfaces

### Concept Overview
Scope-aware conversational interfaces provide natural language interactions while maintaining awareness of the system's knowledge boundaries, gracefully handling out-of-scope questions.

### Problem It Solves
Traditional chatbots either hallucinate answers to unknown questions or provide jarring "I don't know" responses without helpful guidance, leading to poor user experience when users ask questions outside system knowledge.

### Solution Approach
- **Scope Detection**: Determining if questions are answerable within the knowledge domain
- **Graceful Fallbacks**: Providing helpful responses when questions are out of scope
- **Suggested Alternatives**: Recommending related in-scope questions users might want to ask
- **Progressive Discovery**: Helping users understand what the system can and cannot answer

### Implementation Insight
```python
import streamlit as st
from typing import Dict, Any, List

class ScopeAwareChatInterface:
    """Chat interface that handles in-scope and out-of-scope queries gracefully"""
    
    def __init__(self, knowledge_ai, query_processor, knowledge_repo):
        self.knowledge_ai = knowledge_ai
        self.query_processor = query_processor
        self.knowledge_repo = knowledge_repo
    
    def initialize_chat_state(self):
        """Initialize the chat state in the session"""
        if "messages" not in st.session_state:
            st.session_state.messages = []
            
            # Add a welcome message explaining scope
            welcome_message = (
                "👋 Hello! I can answer questions about our leadership team. "
                "You can ask about executives, their roles, departments, and backgrounds. "
                "Try questions like 'Who is the CTO?' or 'What does the VP of Marketing do?'"
            )
            st.session_state.messages.append({"role": "assistant", "content": welcome_message})
    
    def display_chat_history(self):
        """Display the chat history"""
        for message in st.session_state.messages:
            with st.chat_message(message["role"]):
                st.markdown(message["content"])
                
                # If there are profiles to display with this message
                if message.get("profiles"):
                    self._display_mini_profiles(message["profiles"])
    
    def _display_mini_profiles(self, profiles: List[Dict[str, Any]]):
        """Display mini profile cards for relevant people"""
        if not profiles:
            return
            
        st.write("**Relevant People:**")
        
        # Create columns for profile cards
        cols = st.columns(min(len(profiles), 3))
        for i, profile in enumerate(profiles):
            with cols[i % len(cols)]:
                with st.container():
                    if profile.get("photo_url"):
                        st.image(profile["photo_url"], width=100)
                    st.write(f"**{profile['name']}**")
                    st.write(f"{profile['role']}")
                    with st.expander("Bio"):
                        st.write(profile["bio"])
    
    def handle_user_input(self):
        """Process user input and generate appropriate response"""
        if prompt := st.chat_input("Ask about our leadership team..."):
            # Add user message to chat history
            st.session_state.messages.append({"role": "user", "content": prompt})
            
            # Display the user message
            with st.chat_message("user"):
                st.markdown(prompt)
            
            # Display assistant response with loading indicator
            with st.chat_message("assistant"):
                with st.spinner("Thinking..."):
                    try:
                        # Process the query
                        response = self._process_query_and_respond(prompt)
                        
                        # Add response to chat history
                        st.session_state.messages.append(response)
                        
                        # Display response
                        st.markdown(response["content"])
                        
                        # Display any associated profiles
                        if response.get("profiles"):
                            self._display_mini_profiles(response["profiles"])
                            
                    except Exception as e:
                        error_msg = f"Sorry, I encountered an error: {str(e)}"
                        st.error(error_msg)
                        st.session_state.messages.append({"role": "assistant", "content": error_msg})
    
    async def _process_query_and_respond(self, query: str) -> Dict[str, Any]:
        """Process a query and generate an appropriate response"""
        # Get all profiles for processing
        all_profiles = self.knowledge_repo.get_all_profiles()
        
        # Use the query processor to determine relevance and get profiles
        relevant_profiles, is_in_scope = self.query_processor.process_query(query, all_profiles)
        
        if not is_in_scope:
            # Handle out-of-scope query
            suggested_questions = [
                "Who is the CEO?",
                "What is the background of our CTO?",
                "How many people are in the executive team?"
            ]
            
            content = (
                "I'm specifically trained to answer questions about our leadership team. "
                "I don't have information about other topics. Here are some questions you could ask:\n\n" +
                "\n".join(f"- {q}" for q in suggested_questions)
            )
            
            return {
                "role": "assistant",
                "content": content,
                "profiles": []
            }
        
        if not relevant_profiles:
            # No relevant profiles found for an in-scope query
            content = (
                "I don't have specific information to answer that question. "
                "Could you try rephrasing or asking about one of our known leadership team members?"
            )
            
            # Get a sample of profiles to suggest
            sample_profiles = all_profiles[:3] if len(all_profiles) > 3 else all_profiles
            
            return {
                "role": "assistant",
                "content": content,
                "profiles": sample_profiles
            }
        
        # Generate answer from the knowledge AI
        answer_result = await self.knowledge_ai.answer_question(query)
        
        # Get the full profile objects for the relevant profiles
        profile_ids = answer_result.get("relevant_profiles", [])
        profiles = [p for p in all_profiles if p["id"] in profile_ids]
        
        return {
            "role": "assistant",
            "content": answer_result["answer"],
            "profiles": profiles
        }

def main():
    st.title("Leadership Knowledge Base")
    
    # Initialize components
    knowledge_repo = KnowledgeRepository()
    query_processor = IntelligentQueryProcessor()
    knowledge_ai = ContextAwareAI(knowledge_repo, llm_service)
    
    # Create chat interface
    chat_interface = ScopeAwareChatInterface(knowledge_ai, query_processor, knowledge_repo)
    chat_interface.initialize_chat_state()
    
    # Use tabs for different views
    tab1, tab2, tab3 = st.tabs(["Chat", "Browse Profiles", "Data Status"])
    
    with tab1:
        # Display chat interface
        chat_interface.display_chat_history()
        chat_interface.handle_user_input()
        
    with tab2:
        # Browse profiles by department
        departments = knowledge_repo.get_all_departments()
        profiles = knowledge_repo.get_all_profiles()
        display_organized_profiles(profiles, departments)
        
    with tab3:
        # Show data status
        st.header("Knowledge Base Status")
        
        profiles_count = len(knowledge_repo.get_all_profiles())
        departments_count = len(knowledge_repo.get_all_departments())
        
        col1, col2 = st.columns(2)
        with col1:
            st.metric("Total Profiles", profiles_count)
        with col2:
            st.metric("Departments", departments_count)
            
        # Show last updated time (in a real app, this would come from the DB)
        st.write("Last updated: 2023-08-15 14:30")
        
        if st.button("Refresh Data"):
            with st.spinner("Updating knowledge base..."):
                # In a real app, this would trigger a scraping run
                st.success("Knowledge base updated successfully!")

if __name__ == "__main__":
    main()
```
