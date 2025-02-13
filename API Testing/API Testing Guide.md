# API Testing Guide

## What is an API?
An API (Application Programming Interface) acts as an interface between two software systems, enabling them to communicate with each other. A simple analogy is when you go to a restaurant: the waiter acts as an intermediary, taking your orders (requests), delivering them to the chef, and bringing back the prepared dishes (responses). Similarly, an API collects requests and retrieves responses, facilitating interaction between software systems.

## What is API Testing?
API testing is a type of testing where the functionality, reliability, performance, security, and error handling of APIs are verified and validated.

## What is Idempotency?
Idempotency is a property of certain operations in computer science and API design where executing the same operation multiple times produces the same result as executing it once. Examples:
- **Idempotent Methods:** GET, PUT, DELETE
- **Non-Idempotent Method:** POST

## What is JSONPath?
JSONPath is a query language used to traverse and query elements within JSON responses. It helps extract specific values from the response for validation and other purposes.

## What is Request Specification?
Request specifications are predefined configurations for requests, including headers, query parameters, authentication, and more.

## What is BDD in Rest Assured?
BDD (Behavior-Driven Development) in Rest Assured refers to writing tests in a human-readable format. Methods like `given()`, `when()`, and `then()` are used to achieve this, making tests more expressive:
- **Given():** Used to set preconditions.
- **When():** Used to execute the request.
- **Then():** Used to perform assertions on the response.

## What is Authentication?
Authentication is the process of verifying the identity of a user, system, or application. It ensures that only authorized entities can access specific resources or systems.

### Common Authentication Methods:
1. **OAuth 2.0 and JWT:** For modern, scalable, and stateless authentication.
2. **API Keys:** For simple access control.
3. **Mutual TLS:** For high-security environments.

### Authentication vs. Authorization
Authentication verifies identity, while authorization determines access permissions.

## What is a Mock Server?
A mock server is a simulated server that mimics the behavior of a real API. It allows developers and testers to create and test applications without relying on a live API or backend service.

### Benefits of Mock Servers:
- **Parallel Development:** Developers can work on frontend and backend simultaneously.
- **Speed Up Development:** No need to wait for the actual API.
- **Reduce Costs:** Avoid costs associated with testing third-party APIs.

## Query Parameters vs. Path Parameters in an API
- **Query Parameters:** Key-value pairs added to the URL after a `?` symbol, used to filter or modify the data returned by an API.
  - Example: `GET /users?age=25&location=NYC`
- **Path Parameters:** Variables embedded directly within the URL path, often used to specify a particular resource.
  - Example: `GET /users/{userId}`

### Key Difference:
- Query parameters modify the response.
- Path parameters identify or access a specific resource.

## How to Validate the Response Schema of an API
To validate the response schema:
1. **Using Response Specification:**
   ```java
   ResponseSpecBuilder responseSpec = new ResponseSpecBuilder()
       .expectContentType(ContentType.JSON)
       .expectStatusCode(200)
       .build();
   then().spec(responseSpec);
   ```
2. **Using JSON Schema Validator or Postman:** Validate the response body against a predefined JSON schema to ensure correct fields, data types, and structure.

## What is Rate Limiting?
Rate limiting controls the number of requests a client can make to an API within a specific time period, protecting the API from abuse, DDoS attacks, and server overloading.

### Testing Rate Limiting:
1. Simulate multiple requests using tools like JMeter, RestAssured, or Locust.
2. Validate status codes (e.g., 429 Too Many Requests).

### API Response Codes:
- **1xx:** Informational
- **2xx:** Success
- **3xx:** Redirection
- **4xx:** Client Error
- **5xx:** Server Error

## Key Elements in Designing an API Test Suite
- Identify APIs to be tested and their endpoints.
- Determine required test data and input parameters.
- Define expected outcomes or responses.
- Ensure coverage for various scenarios, error conditions, and edge cases.
- Incorporate test environment setup and teardown processes.
- Address authentication or authorization mechanisms.
- Include performance testing, security testing, and error handling.

## Ensuring API Test Coverage
Techniques and tools:
- Analyze API documentation for all endpoints and methods.
- Use code coverage tools like SonarQube.
- Track and manage tests with test case management tools.
- Apply equivalence partitioning and boundary value analysis.
- Perform exploratory testing.
- Use contract testing to ensure compatibility between API consumers and providers.

## Common Challenges in API Testing and Solutions
1. **Test Data Management:** Use generated data or mock servers.
2. **Authentication and Authorization Complexities:** Collaborate with the development team for proper implementation.
3. **API Stability Issues:** Implement retry mechanisms, handle timeouts, and design tests to manage failures.

## Process for Testing an API for the First Time
1. Understand the API documentation.
2. Design test cases.
3. Set up the test environment.
4. Execute test cases and validate responses.
5. Log issues, if any.

## Testing an API for Performance
Performance testing types:
- **Load Testing:** Simulate expected traffic.
- **Stress Testing:** Identify breaking points.
- **Spike Testing:** Test sudden traffic surges.
- **Endurance Testing:** Evaluate performance over prolonged durations.
- **Latency Testing:** Measure request-response delays.

### Tools for Performance Testing:
- JMeter
- Postman
- Locust
- Gatling

## Debugging an API with Unexpected Responses
Steps:
1. Analyze the response (status code, headers, body).
2. Verify request specifications (parameters, headers, payload).
3. Check server logs and dependencies.
4. Replicate the issue in a controlled environment.
5. Use tools like Postman or cURL.

## Handling API Versioning in Testing
API versioning ensures changes do not break compatibility for existing users.

### Types of API Versioning:
- **URL Versioning**
- **Query Parameter Versioning**

