**Candidate:** Rushitha Chittibomma  
**Reviewer:** AI Code Reviewer  
**Date:** August 22, 2025  
**Project:** Chatbot Milestone 2  

# Error Handling & Robustness Review for Rushitha Chittibomma

**Overall Robustness Score: POOR (3/10)**

This review focuses on the application's ability to handle errors, validate inputs, and manage edge cases gracefully. The codebase currently has **major weaknesses** in this area, making it fragile and susceptible to crashes from predictable issues like API failures or invalid configuration.

---

### 1. Exception Handling Quality
- **Weaknesses**:
    - **Overly Broad Exception Handling**: The `llm_service.py` uses a generic `except Exception as e:` block. This is a significant anti-pattern because it catches *every* possible error, including programming errors (like `NameError` or `TypeError`) that should ideally crash the program during development so they can be fixed. It also hides the specific nature of the failure, making debugging difficult.
    - **No Specific API Error Handling**: The code does not handle specific exceptions that the Google Gemini API might raise. What happens if the API key is invalid? Or if the rate limit is exceeded? Or if the service is temporarily unavailable? The current implementation would catch these and simply return `None`, giving the user no feedback on what went wrong.
    - **No Error Handling for File I/O**: The `chat_manager.py` reads and writes to a JSON file without any `try...except` blocks. This is very risky. The application will crash if the file is corrupted, if the disk is full, or if there are permission errors.

    **Recommendation**: Implement specific, multi-layered exception handling.

    **Before (in `llm_service.py`):**
    ```python
    try:
        response = model.generate_content(prompt)
        return response.text
    except Exception as e:
        st.error(f"An error occurred: {e}")
        return None
    ```

    **After (Conceptual - requires Google AI library knowledge):**
    ```python
    # Assuming the library has specific exceptions, which it does.
    # e.g., google.api_core.exceptions.PermissionDenied, google.api_core.exceptions.ResourceExhausted
    from google.api_core import exceptions as google_exceptions

    try:
        response = model.generate_content(prompt)
        return response.text
    except google_exceptions.PermissionDenied as e:
        st.error("Authentication error: Please check your API key.")
        # Potentially disable the chat input here
        return None
    except google_exceptions.ResourceExhausted as e:
        st.error("API rate limit exceeded. Please try again later.")
        return None
    except Exception as e:
        # A fallback for unexpected errors
        st.error("An unexpected error occurred. Please contact support.")
        # Log the full error for debugging
        print(f"UNEXPECTED ERROR: {e}")
        return None
    ```

---

### 2. Input Validation & Sanitization
- **Weaknesses**:
    - **No Input Validation**: User input from the Streamlit text box is passed directly to the `llm_service.py` and then to the Gemini API. There is no check for empty strings, excessively long inputs, or malicious content.
    - **Security Risk (Prompt Injection)**: The lack of sanitization makes the application vulnerable to prompt injection. A malicious user could enter text that instructs the underlying LLM to ignore previous instructions or perform unintended actions.

    **Recommendation**:
    - Add basic validation in `app.py` before calling the LLM service.
    - For a more robust solution, consider using a library like `guardrails-ai` or implementing sanitization logic to strip out potential control characters or instruction-like text.

    **Example (in `app.py`):**
    ```python
    if prompt := st.chat_input("What is up?"):
        # Basic validation
        if not prompt.strip():
            st.warning("Please enter a message.")
        elif len(prompt) > 2000: # Example length limit
            st.warning("Your message is too long. Please keep it under 2000 characters.")
        else:
            # Add message to history and call LLM
            # ...
    ```

---

### 3. Edge Case & Boundary Handling
- **Weaknesses**:
    - **Missing API Key**: The `config.py` loads the API key from an environment variable. If the variable is not set, `os.getenv("GEMINI_API_KEY")` will return `None`. This `None` value is then passed to the `LLMService`, which will likely cause the `genai.configure` call to fail with an unhandled exception.
    - **Initial State**: The application correctly checks for `st.session_state.messages` existence, which is good. However, it doesn't handle the case where the chat history file in `chat_manager.py` might be empty or contain invalid JSON. The `json.load()` call would crash the app.

    **Recommendation**:
    - Add explicit checks for configuration and environment variables at startup. Fail fast with a clear error message if critical configuration is missing.
    - Wrap file parsing in `try...except json.JSONDecodeError` to handle corrupted files gracefully.

    **Example (in `config.py` or `app.py` startup):**
    ```python
    API_KEY = os.getenv("GEMINI_API_KEY")
    if not API_KEY:
        st.error("FATAL: GEMINI_API_KEY environment variable is not set.")
        st.stop() # Stop the app from running further
    ```

---

### 4. Logging & Error Reporting
- **Weaknesses**:
    - **No Logging**: There is no structured logging. Errors are either printed to the console (`print`) or shown directly to the user (`st.error`). This is insufficient for a real application, as there is no persistent record of when or why failures occurred.

    **Recommendation**:
    - Introduce Python's built-in `logging` module.
    - Configure it to log errors to a file, including timestamps and stack traces. This is crucial for debugging issues in a deployed environment.

### Summary & Key Recommendations:
1.  **Implement Specific Exception Handling**: This is the highest priority. Refactor all `try...except` blocks to catch specific, anticipated errors (`PermissionDenied`, `FileNotFoundError`, `JSONDecodeError`) first, and use a generic `except Exception` only as a final fallback.
2.  **Add Input Validation**: Add checks in `app.py` to validate user input for length and content before sending it to the LLM.
3.  **Validate Configuration on Startup**: Add a "pre-flight check" to ensure that essential configurations like the API key are present. If not, stop the application with a clear error message.
4.  **Introduce Logging**: Add the `logging` module to provide a structured way to record errors for later analysis.
