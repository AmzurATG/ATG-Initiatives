# Project 11: Model Context Protocol (MCP) - Key Concepts

> **Note:** This document establishes the foundational MCP concepts for bootcamp students that enable AI assistants to access external data safely and efficiently.

## 1. Simple MCP Server Architecture & Data Integration

### Concept Overview
Model Context Protocol (MCP) is a standardized way for AI assistants to securely access external data sources like databases and files. MCP servers act as safe bridges between AI systems and your data, providing controlled access with proper security and validation.

### Problem It Solves
AI assistants like Claude and ChatGPT can only work with the information in their training data or what you provide in a conversation. They can't access your databases, read your files, or get current information. MCP solves this by creating secure servers that give AI controlled access to external data while maintaining security and privacy.

### Business Benefits & Value Proposition
**Quantifiable Benefits:**
- **Enhanced AI Capabilities**: AI can now work with your actual data, not just general knowledge
- **Automated Data Access**: 80% reduction in manual data retrieval for AI interactions
- **Secure Integration**: Controlled, auditable access to sensitive data sources
- **Standardized Protocol**: Industry-standard approach for AI-data integration

**Educational Impact Examples:**
- Students understand how modern AI systems access real-world data
- Practical experience with secure data integration patterns
- Foundation for understanding AI system architecture
- Portfolio project demonstrating advanced AI integration skills

**Learning Advantages:**
- Hands-on experience with emerging AI protocols
- Understanding of secure AI-data integration patterns
- Practical skills in API development and data security
- Preparation for AI infrastructure engineering roles

### Solution Approach
- **Standardized Protocol**: Using official MCP specification for compatibility
- **Simple Server Architecture**: Basic database and file servers for learning
- **Security-First Design**: Input validation and safe data access patterns
- **Step-by-Step Learning**: Daily progression from basic to integrated systems
- **Practical Applications**: Real data sources that students can relate to

### Implementation Insight
```python
from mcp import MCPServer, Resource, Tool
import sqlite3
import os
from pathlib import Path
import json

class SimpleDatabaseMCPServer(MCPServer):
    """
    Simple MCP server for database access - focuses on learning MCP fundamentals
    """
    
    def __init__(self, name: str = "database-server", version: str = "1.0.0"):
        super().__init__(name, version)
        self.db_path = "data/sample_business.db"
        self.setup_sample_database()
        self.register_resources()
        self.register_tools()
    
    def setup_sample_database(self):
        """Create sample business database with customers and orders"""
        os.makedirs("data", exist_ok=True)
        
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()
        
        # Create customers table
        cursor.execute('''
        CREATE TABLE IF NOT EXISTS customers (
            id INTEGER PRIMARY KEY,
            name TEXT NOT NULL,
            email TEXT UNIQUE,
            phone TEXT,
            city TEXT,
            created_date DATE
        )
        ''')
        
        # Create orders table
        cursor.execute('''
        CREATE TABLE IF NOT EXISTS orders (
            id INTEGER PRIMARY KEY,
            customer_id INTEGER,
            product_name TEXT,
            quantity INTEGER,
            total_amount DECIMAL(10,2),
            order_date DATE,
            FOREIGN KEY (customer_id) REFERENCES customers (id)
        )
        ''')
        
        # Insert sample data
        sample_customers = [
            (1, 'John Smith', 'john@email.com', '555-0101', 'New York', '2024-01-15'),
            (2, 'Jane Doe', 'jane@email.com', '555-0102', 'Los Angeles', '2024-01-20'),
            (3, 'Bob Johnson', 'bob@email.com', '555-0103', 'Chicago', '2024-02-01')
        ]
        
        sample_orders = [
            (1, 1, 'Laptop Computer', 1, 999.99, '2024-02-15'),
            (2, 1, 'Wireless Mouse', 2, 49.98, '2024-02-16'),
            (3, 2, 'Office Chair', 1, 299.99, '2024-02-20'),
            (4, 3, 'Desk Lamp', 3, 89.97, '2024-02-25')
        ]
        
        cursor.executemany('INSERT OR REPLACE INTO customers VALUES (?,?,?,?,?,?)', sample_customers)
        cursor.executemany('INSERT OR REPLACE INTO orders VALUES (?,?,?,?,?,?)', sample_orders)
        
        conn.commit()
        conn.close()
        print(f"Sample database created at {self.db_path}")
    
    def register_resources(self):
        """Register database tables as MCP resources"""
        # Register customers table as a resource
        customers_resource = Resource(
            uri="database://customers",
            name="Customer Database",
            description="Customer information including contact details and registration dates",
            mimeType="application/json"
        )
        
        # Register orders table as a resource
        orders_resource = Resource(
            uri="database://orders", 
            name="Orders Database",
            description="Order history with product information and purchase amounts",
            mimeType="application/json"
        )
        
        self.add_resource(customers_resource)
        self.add_resource(orders_resource)
    
    def register_tools(self):
        """Register database query tools for AI use"""
        # Customer search tool
        customer_search_tool = Tool(
            name="search_customers",
            description="Search customers by name, email, or city. Use this to find customer information.",
            inputSchema={
                "type": "object",
                "properties": {
                    "search_term": {
                        "type": "string",
                        "description": "Search term for customer name, email, or city"
                    }
                },
                "required": ["search_term"]
            }
        )
        
        # Order lookup tool
        order_lookup_tool = Tool(
            name="get_orders",
            description="Get orders for a specific customer or all recent orders",
            inputSchema={
                "type": "object", 
                "properties": {
                    "customer_id": {
                        "type": "integer",
                        "description": "Customer ID to get orders for (optional)"
                    },
                    "limit": {
                        "type": "integer",
                        "description": "Maximum number of orders to return",
                        "default": 10
                    }
                }
            }
        )
        
        self.add_tool(customer_search_tool)
        self.add_tool(order_lookup_tool)
    
    async def search_customers(self, search_term: str) -> str:
        """Search customers safely with input validation"""
        try:
            # Input validation
            if not search_term or len(search_term.strip()) < 2:
                return "Error: Search term must be at least 2 characters"
            
            clean_search = search_term.strip().lower()
            
            conn = sqlite3.connect(self.db_path)
            cursor = conn.cursor()
            
            # Safe parameterized query to prevent SQL injection
            query = '''
            SELECT id, name, email, city, created_date 
            FROM customers 
            WHERE LOWER(name) LIKE ? OR LOWER(email) LIKE ? OR LOWER(city) LIKE ?
            '''
            
            search_pattern = f"%{clean_search}%"
            cursor.execute(query, (search_pattern, search_pattern, search_pattern))
            results = cursor.fetchall()
            
            conn.close()
            
            if not results:
                return f"No customers found matching '{search_term}'"
            
            # Format results for AI consumption
            customers = []
            for row in results:
                customers.append({
                    "id": row[0],
                    "name": row[1], 
                    "email": row[2],
                    "city": row[3],
                    "created_date": row[4]
                })
            
            return json.dumps({
                "customers_found": len(customers),
                "customers": customers
            }, indent=2)
            
        except Exception as e:
            return f"Database error: {str(e)}"
    
    async def get_orders(self, customer_id: int = None, limit: int = 10) -> str:
        """Get orders with proper validation"""
        try:
            conn = sqlite3.connect(self.db_path)
            cursor = conn.cursor()
            
            if customer_id:
                # Get orders for specific customer
                query = '''
                SELECT o.id, o.product_name, o.quantity, o.total_amount, o.order_date, c.name
                FROM orders o
                JOIN customers c ON o.customer_id = c.id
                WHERE o.customer_id = ?
                ORDER BY o.order_date DESC
                LIMIT ?
                '''
                cursor.execute(query, (customer_id, limit))
            else:
                # Get all recent orders
                query = '''
                SELECT o.id, o.product_name, o.quantity, o.total_amount, o.order_date, c.name
                FROM orders o  
                JOIN customers c ON o.customer_id = c.id
                ORDER BY o.order_date DESC
                LIMIT ?
                '''
                cursor.execute(query, (limit,))
            
            results = cursor.fetchall()
            conn.close()
            
            if not results:
                return "No orders found"
            
            orders = []
            for row in results:
                orders.append({
                    "order_id": row[0],
                    "product": row[1],
                    "quantity": row[2], 
                    "amount": float(row[3]),
                    "date": row[4],
                    "customer": row[5]
                })
            
            return json.dumps({
                "orders_found": len(orders),
                "orders": orders
            }, indent=2)
            
        except Exception as e:
            return f"Database error: {str(e)}"

class SimpleFileMCPServer(MCPServer):
    """
    Simple MCP server for file access - focuses on safe document reading
    """
    
    def __init__(self, name: str = "file-server", version: str = "1.0.0"):
        super().__init__(name, version)
        self.safe_directory = Path("documents")
        self.setup_sample_documents()
        self.register_resources()
        self.register_tools()
    
    def setup_sample_documents(self):
        """Create sample documents for testing"""
        self.safe_directory.mkdir(exist_ok=True)
        
        # Company policy document
        policy_content = """
        COMPANY POLICIES AND PROCEDURES
        
        1. CUSTOMER SERVICE POLICY
        - Respond to all customer inquiries within 24 hours
        - Maintain professional and helpful communication
        - Escalate complex issues to management
        
        2. RETURN POLICY
        - 30-day return window for all products
        - Items must be in original condition
        - Refunds processed within 5-7 business days
        
        3. SHIPPING POLICY
        - Free shipping on orders over $50
        - Express shipping available for additional fee
        - International shipping to select countries
        
        Last updated: January 2024
        """
        
        with open(self.safe_directory / "company_policies.txt", "w") as f:
            f.write(policy_content)
        
        # Product catalog
        catalog_content = """
        PRODUCT CATALOG - OFFICE SUPPLIES
        
        COMPUTERS & LAPTOPS
        - Business Laptop Pro: $999.99
        - Desktop Computer: $599.99
        - Tablet Device: $299.99
        
        ACCESSORIES
        - Wireless Mouse: $24.99
        - Keyboard: $49.99
        - Monitor Stand: $89.99
        
        FURNITURE
        - Office Chair: $299.99
        - Standing Desk: $499.99
        - Desk Lamp: $29.99
        
        Contact sales@company.com for bulk pricing
        """
        
        with open(self.safe_directory / "product_catalog.txt", "w") as f:
            f.write(catalog_content)
        
        print(f"Sample documents created in {self.safe_directory}")
    
    def register_resources(self):
        """Register documents as MCP resources"""
        for file_path in self.safe_directory.glob("*.txt"):
            resource = Resource(
                uri=f"file://{file_path.name}",
                name=file_path.stem.replace("_", " ").title(),
                description=f"Document: {file_path.name}",
                mimeType="text/plain"
            )
            self.add_resource(resource)
    
    def register_tools(self):
        """Register file reading tools for AI use"""
        read_file_tool = Tool(
            name="read_file",
            description="Read and return the contents of a text file",
            inputSchema={
                "type": "object",
                "properties": {
                    "filename": {
                        "type": "string",
                        "description": "Name of the file to read (e.g., 'company_policies.txt')"
                    }
                },
                "required": ["filename"]
            }
        )
        
        list_files_tool = Tool(
            name="list_files", 
            description="List all available documents",
            inputSchema={
                "type": "object",
                "properties": {}
            }
        )
        
        self.add_tool(read_file_tool)
        self.add_tool(list_files_tool)
    
    async def read_file(self, filename: str) -> str:
        """Read file contents safely"""
        try:
            # Security validation
            if not filename or '..' in filename or '/' in filename:
                return "Error: Invalid filename. Use simple filenames only."
            
            file_path = self.safe_directory / filename
            
            # Ensure file is within safe directory
            if not str(file_path.resolve()).startswith(str(self.safe_directory.resolve())):
                return "Error: Access denied. File outside safe directory."
            
            if not file_path.exists():
                available_files = [f.name for f in self.safe_directory.glob("*.txt")]
                return f"Error: File '{filename}' not found. Available files: {', '.join(available_files)}"
            
            with open(file_path, 'r', encoding='utf-8') as f:
                content = f.read()
            
            return f"File: {filename}\n\n{content}"
            
        except Exception as e:
            return f"Error reading file: {str(e)}"
    
    async def list_files(self) -> str:
        """List available documents"""
        try:
            files = list(self.safe_directory.glob("*.txt"))
            if not files:
                return "No documents available"
            
            file_list = []
            for file_path in files:
                file_info = {
                    "filename": file_path.name,
                    "description": file_path.stem.replace("_", " ").title()
                }
                file_list.append(file_info)
            
            return json.dumps({
                "available_files": len(file_list),
                "files": file_list
            }, indent=2)
            
        except Exception as e:
            return f"Error listing files: {str(e)}"

# Simple MCP client for testing
class SimpleMCPClient:
    """Simple MCP client for connecting AI assistants to servers"""
    
    def __init__(self):
        self.servers = {}
        
    def register_server(self, name: str, server: MCPServer):
        """Register an MCP server"""
        self.servers[name] = server
        print(f"Registered MCP server: {name}")
    
    def get_available_resources(self):
        """Get all available resources from registered servers"""
        resources = {}
        for server_name, server in self.servers.items():
            resources[server_name] = server.get_resources()
        return resources
    
    def get_available_tools(self):
        """Get all available tools from registered servers"""
        tools = {}
        for server_name, server in self.servers.items():
            tools[server_name] = server.get_tools()
        return tools
    
    async def call_tool(self, server_name: str, tool_name: str, **kwargs):
        """Call a tool on a specific server"""
        if server_name not in self.servers:
            return f"Server '{server_name}' not found"
        
        server = self.servers[server_name]
        return await server.call_tool(tool_name, **kwargs)

# Usage example for bootcamp learning
def demonstrate_mcp_system():
    """Complete example showing MCP system in action"""
    
    print("=== MCP System Demo ===")
    
    # Create MCP servers
    db_server = SimpleDatabaseMCPServer()
    file_server = SimpleFileMCPServer()
    
    # Create client and register servers
    client = SimpleMCPClient()
    client.register_server("database", db_server)
    client.register_server("files", file_server)
    
    print("\nAvailable Resources:")
    resources = client.get_available_resources()
    for server, server_resources in resources.items():
        print(f"  {server}: {len(server_resources)} resources")
    
    print("\nAvailable Tools:")
    tools = client.get_available_tools()
    for server, server_tools in tools.items():
        print(f"  {server}: {[tool.name for tool in server_tools]}")
    
    return client, db_server, file_server

# Example AI assistant integration
async def ai_assistant_example():
    """Example showing how AI assistant would use MCP"""
    
    client, db_server, file_server = demonstrate_mcp_system()
    
    print("\n=== AI Assistant Using MCP ===")
    
    # AI asks: "Find customer John and show his orders"
    print("AI Request: Find customer John and show his orders")
    
    # Step 1: Search for customer
    customer_result = await client.call_tool("database", "search_customers", search_term="John")
    print("Customer search result:", customer_result)
    
    # Step 2: Get orders for customer
    order_result = await client.call_tool("database", "get_orders", customer_id=1, limit=5)
    print("Order lookup result:", order_result)
    
    # Step 3: Read company policies
    policy_result = await client.call_tool("files", "read_file", filename="company_policies.txt")
    print("Policy document:", policy_result[:200] + "...")
    
    return "AI successfully accessed database and file data through MCP!"
```

### Key Performance Parameters (Bootcamp Level)
**Metrics to Track:**
- **Data Access Success**: MCP servers respond correctly > 95% of the time
- **Security Compliance**: Input validation prevents unauthorized access
- **Protocol Adherence**: Proper MCP message format and responses
- **AI Integration**: AI assistants can successfully use MCP resources
- **Error Handling**: Clear, helpful error messages for invalid requests

**Learning Benchmarks:**
- Day 1: Database MCP server working with AI assistant queries
- Day 2: File MCP server safely exposing documents to AI
- Day 3: Complete system with AI using both database and file data
- Portfolio readiness: Professional demo suitable for job applications

**Success Indicators:**
- **Multi-Source Access**: AI can query both database and files in single interaction
- **Secure Operations**: Proper input validation and access controls working
- **Real-World Utility**: Solves actual problems people would encounter
- **Technical Understanding**: Students can explain MCP protocol and security

### Common Pitfalls & Anti-Patterns (Bootcamp Context)
- **Poor Security**: Not validating inputs or allowing unrestricted file access
- **Protocol Violations**: Not following MCP specification correctly
- **Overly Complex Servers**: Building enterprise features instead of learning basics
- **No Error Handling**: MCP servers failing without helpful error messages
- **Hardcoded Paths**: Using absolute paths that don't work on other machines
- **Missing Validation**: Accepting any input without safety checks

### Cross-References & Dependencies (Simplified)
- **Builds on Project 9**: MCP can provide data for ML model training and inference
- **Integrates with Project 10**: AI agents can use MCP servers as data sources
- **Supports Project 16**: Capstone uses MCP for comprehensive data integration
- **Complements Project 12**: N8N workflows can trigger MCP data operations
- **Foundation Knowledge**: API development, database basics, file handling, security concepts

### Architecture Diagram (Simplified)
```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│                 │    │                  │    │                 │
│  AI Assistant   │───▶│  MCP Client      │───▶│   MCP Servers   │
│                 │    │                  │    │                 │
│ • Claude        │    │ • Protocol       │    │ • Database      │
│ • ChatGPT       │    │ • Message Route  │    │ • File System   │
│ • Custom Agent  │    │ • Error Handle   │    │ • API Gateway   │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                │                        │
                                │                        │
┌─────────────────┐    ┌──────────────────┐    ┌─────────▼───────┐
│                 │    │                  │    │                 │
│  Data Response  │◀───│  Security Layer  │◀───│  Data Sources   │
│                 │    │                  │    │                 │
│ • Formatted     │    │ • Input Valid    │    │ • SQLite DB     │
│ • Structured    │    │ • Access Control │    │ • Text Files    │
│ • AI-Friendly   │    │ • Audit Logging  │    │ • Documents     │
└─────────────────┘    └──────────────────┘    └─────────────────┘
```

### Scalability & Production Considerations (Bootcamp Perspective)
**Learning Progression:**
- **Start Simple**: Basic database and file servers with sample data
- **Add Security**: Input validation and access controls
- **Expand Capabilities**: More data sources and tool types
- **Build Portfolio**: Create impressive demos for job applications

**Resource Requirements (Local Development):**
- **Simple Setup**: SQLite database and local files
- **Popular Tools**: Standard Python libraries and MCP SDK
- **Local Testing**: No cloud infrastructure required
- **AI Integration**: Compatible with Claude, ChatGPT, custom agents

**Skills Development:**
- **Protocol Understanding**: Learning standardized AI-data communication
- **Security Awareness**: Safe data exposure patterns and validation
- **API Development**: Building servers that AI systems can use
- **System Integration**: Connecting AI to real-world data sources

### Industry Use Cases & Examples (Bootcamp Context)
**Learning-Friendly Applications:**
- **Customer Support**: AI accessing customer database and knowledge base
- **Business Intelligence**: AI analyzing business data and generating insights
- **Document Processing**: AI reading and summarizing business documents
- **Data Analysis**: AI querying databases for reporting and analytics

**Portfolio Examples:**
- **Smart Help Desk**: AI assistant with access to customer data and policies
- **Business Analyst**: AI that can query databases and read reports
- **Knowledge Assistant**: AI with access to company documents and procedures
- **Data Explorer**: AI that helps users understand and analyze business data

**Career Relevance:**
- **AI Engineering**: Understanding AI system architecture and integration
- **Data Engineering**: Building data access layers for AI systems
- **Backend Development**: Creating APIs and data services
- **DevOps**: Deploying and managing AI infrastructure components

### Further Resources (Bootcamp Friendly)
- [Model Context Protocol Specification](https://github.com/anthropics/mcp) - Official MCP documentation
- [MCP Python SDK](https://pypi.org/project/mcp/) - Python implementation and examples
- [SQLite Tutorial](https://www.sqlitetutorial.net/) - Database fundamentals for MCP servers
- [File Security Best Practices](https://owasp.org/www-community/vulnerabilities/Path_Traversal) - Safe file handling patterns
- [API Security Guidelines](https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html) - Input validation and security

### Assessment & Progress Tracking
**Daily Checkpoints:**
- **Day 1**: Database MCP server working with AI queries
- **Day 2**: File MCP server safely reading documents  
- **Day 3**: Complete integration with AI assistant using both servers

**Mastery Indicators:**
- **Explains MCP Protocol**: Can describe how MCP enables AI-data integration
- **Implements Security**: Proper input validation and access controls
- **Debugs Integration**: Can identify and fix AI-server communication issues
- **Designs Data Access**: Can plan MCP architecture for new data sources

**Portfolio Readiness:**
- **Working Demo**: AI assistant successfully using MCP servers for real queries
- **Technical Documentation**: Clear explanation of MCP implementation and security
- **Business Application**: Solves real problems with clear value proposition
- **Professional Presentation**: Suitable for technical interviews and job applications

### Advanced Learning Extensions (Optional)
**If Time Permits:**
- **Additional Data Sources**: REST APIs, cloud databases, external services
- **Advanced Security**: OAuth integration, role-based access control
- **Performance Optimization**: Caching, connection pooling, response optimization
- **Enterprise Features**: Multi-tenant support, audit logging, compliance features
- **Custom Protocols**: Extending MCP for specialized data types or operations
