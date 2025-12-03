# API Security Review

This document reviews the security of the application's API-like interfaces. While the project is not a traditional REST API, its backend services and controllers act as an internal API for the Streamlit frontend.

---

### Endpoint Security Analysis
- **"Endpoints"**: The "endpoints" in this context are the functions within `chat_controller.py` that are called by the Streamlit UI in `app.py`.
- **Authentication**: As established in the "Authentication & Authorization" review, there is no authentication on these endpoints.
- **Authorization**: There is no authorization. Any part of the frontend code can call any controller function.
- **Parameter Validation**: As established in the "Input Validation" review, there is no validation of the parameters (e.g., `user_input`) passed to these controller functions.

**Assessment**: The internal API endpoints lack fundamental security controls.
**Score: 2/10 (Critical)**

---

### Rate Limiting & DoS Protection
- **Request Rate Limiting**: **CRITICAL VULNERABILITY**. There is no rate limiting of any kind. A malicious user or a simple script could repeatedly call the `get_bot_response` function in a tight loop.
- **Impact**:
    1.  **Denial of Service (DoS)**: The application would become slow or unresponsive for other users as it tries to process a flood of requests.
    2.  **Economic Exhaustion**: Each call to `get_bot_response` results in a call to the external (and paid) Google Gemini API. An attacker could rapidly drive up costs, potentially exhausting the API budget and causing financial harm.
- **Resource Consumption**: There are no controls on the length of user input or the size of the conversation history being processed, which could also be abused to increase resource consumption.

**Assessment**: The lack of rate limiting is a critical vulnerability that exposes the application to both DoS and financial attacks.
**Score: 1/10 (Critical)**

---

### CORS & Cross-Origin Security
- **CORS Policy**: This is not applicable in the current architecture. The "API" is not a set of HTTP endpoints exposed to different origins. The Streamlit frontend and the Python backend are part of the same running process and origin.

**Assessment**: Not applicable.
**Score: N/A**

---

### API Authentication Security
- **API Key Management**: The `GEMINI_API_KEY` is the only API credential involved. It is managed correctly using a `.env` file and is not exposed to the client. This is a strong point.
- **Internal API Authentication**: There is no authentication for the internal API (the controller functions).

**Assessment**: External API key management is secure. Internal API security is non-existent.
**Score: 8/10 (Good)** - Scored high due to the correct handling of the critical external API key.

---

## Overall API Security Score: 3/10 (Needs Improvement)

### Summary & Recommendations

The application's internal API is critically insecure due to a complete lack of rate limiting, which exposes it to denial of service and economic exhaustion attacks. While the external API key is handled well, the internal "endpoints" have no security controls.

**Recommendations:**

1.  **Implement Per-Session Rate Limiting**:
    *   **Action**: Introduce a rate-limiting mechanism to control how frequently a single user (identified by `session_id`) can make requests.
    *   **Implementation**: Use a library like `ratelimit` or a simple in-memory store (for a single-instance app) to track request timestamps for each `session_id`.
    *   **Example (in `app.py` or `chat_controller.py`):**
        ```python
        from ratelimit import limits, RateLimitException
        from time import sleep

        # Limit to 15 calls per minute (900 seconds)
        @limits(calls=15, period=900)
        def get_response_with_rate_limit(user_input, session_id):
            # This function would wrap the actual call to the controller
            return chat_controller.get_bot_response(user_input, session_id)

        # In the main UI loop:
        try:
            bot_response = get_response_with_rate_limit(prompt, st.session_state.session_id)
            st.markdown(bot_response)
        except RateLimitException:
            st.error("You are sending messages too quickly. Please wait a moment and try again.")
        ```

2.  **Enforce Input Size Limits**:
    *   **Action**: Add server-side validation to enforce a maximum character limit on user input.
    *   **Implementation**: Before processing the input in `chat_controller.py`, check its length.
    *   **Example**:
        ```python
        def get_bot_response(user_input, session_id):
            MAX_INPUT_LENGTH = 2000
            if len(user_input) > MAX_INPUT_LENGTH:
                return "Error: Your message is too long. Please keep it under 2000 characters."
            # ... rest of the function
        ```
    *   **Impact**: This prevents users from submitting excessively long prompts, which can be costly and slow to process.
