# Prompt Engineering & AI Input Design Review

## Project Information
- **Candidate Name:** Revanth
- **Project Title:** LLM Chatbot
- **Review Date:** 2023-10-27
- **Reviewer:** GitHub Copilot

---

### Prompt Design Quality & Effectiveness
- **Structure and Clarity**: The prompt structure is basic but clear, using the standard "system" and "user" roles.
- **Instruction Specificity**: The system prompt, "You are a helpful assistant," is very generic. It does not provide specific instructions, constraints, or a desired output format, which can lead to inconsistent or verbose responses.
- **Context Provision**: No additional context is provided beyond the single user question.

### Dynamic Prompt Construction
- The only dynamic part of the prompt is the user's question. The system prompt is static.

### Prompt Template Management
- **No Templating**: Prompts are hardcoded directly in the `app.py` file. For a more maintainable or complex application, these should be extracted into a separate configuration file or a dedicated prompt management system.

### Context Management & Conversation Flow
- **Stateless**: The application is stateless. It does not manage conversation history. Each question is treated as a new, independent conversation. This means the user cannot ask follow-up questions that refer to previous turns.

### Token Optimization & Cost Management
- **No Optimization**: There is no attempt to manage or optimize token count.

### Safety & Security in Prompt Design
- **No Sanitization**: The user's input is passed directly to the API without any sanitization or checks for prompt injection patterns.

**Prompt Engineering Scoring:**
- **BASIC (3/10)**: The prompt engineering is at a very foundational level. It is functional for a simple Q&A bot but lacks the techniques required for building a robust, context-aware, or specialized AI feature.

**Recommendations**:
1.  **Improve System Prompt**: Make the system prompt more specific. For example: "You are a concise and helpful assistant. Answer the user's question directly and clearly."
2.  **Implement Conversation History**: To enable follow-up questions, store the last few turns of the conversation in the user's session and include them in the `messages` array on subsequent requests.
3.  **Externalize Prompts**: Move the system prompt string out of the code and into a configuration variable.
