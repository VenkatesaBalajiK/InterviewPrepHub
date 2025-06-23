# 📚 API Testing Notes

## 📇 Table of Contents

- [📌 Lesson 1: What Is an API?](#-lesson-1-what-is-an-api)
- [📌 Lesson 2: The Anatomy of a Request & Response](#-lesson-2-the-anatomy-of-a-request--response)
- [📌 Lesson 3: HTTP Methods – CRUD Breakdown in Real-World APIs](#-lesson-3-http-methods--crud-breakdown-in-real-world-apis)
- [📌 Lesson 4: Authentication and Authorization](#-lesson-4-authentication-and-authorization)
- [📌 Lesson 7: Headers & Cookies in API Testing](#-lesson-7-headers--cookies-in-api-testing)
- [📌 Glossary](#-glossay)
- [📌 Summary](#-summary)

## 📌 **Lesson 1: What Is an API?**

### **API = Application Programming Interface.**

*Think of it as a waiter in a restaurant. You (the client) place your order (a request), the waiter (API) delivers it to the kitchen (server), and comes back with your food (response).*

#### In Tech Terms:

* The **client** is your application or test tool (like Postman, Rest Assured).
* The **server** is the backend system providing services/data.
* The **API** is the middleman – it processes your request and brings you back a response.

---

### 🧱 **Types of APIs (Focus: Web APIs)**

We're going to focus on **Web APIs**, particularly **REST APIs**, because they’re everywhere.

* **REST (Representational State Transfer)**: Uses HTTP methods (GET, POST, PUT, DELETE)
* **SOAP (Simple Object Access Protocol)**: XML-based, heavier, older
* **GraphQL**: Query-based, flexible, modern – not our priority *right now*

---

### 🎯 **Core HTTP Methods in REST APIs**

| Method | Purpose     | Example Use Case          |
| ------ | ----------- | ------------------------- |
| GET    | Read data   | Fetch user details        |
| POST   | Create data | Add a new user            |
| PUT    | Update data | Update user profile       |
| DELETE | Delete data | Remove user from database |

---

## 📌 **Lesson 2: The Anatomy of a Request & Response**
### 🔍 **API Request – The Anatomy of a Request**

When you hit an API, you’re basically constructing an HTTP message. Let’s break it into 5 core parts:

---

### 1. **Request URL (Endpoint) – The Door You Knock On**

> `https://api.example.com/users/123`

* **Protocol**: `https` (secure version of HTTP)
* **Base URL**: `api.example.com` (the server)
* **Path**: `/users/123` (resource you're targeting – user with ID 123)
* **Query Params**: `?active=true&sort=desc` (optional filters)

👉 **Example with query params:**

```http
GET https://api.example.com/users?active=true&sort=desc
```

---

### 2. **HTTP Method – Your Intent**

* **GET**: I want to *fetch* something.
* **POST**: I want to *create* something.
* **PUT**/**PATCH**: I want to *update* something.
* **DELETE**: I want to *remove* something.

These are the verbs of your testing language. REST APIs are just conversations in this dialect.

---

### 3. **Headers – Your Credentials & Metadata**

Think of headers as your business card + protocol agreement.

Some key headers:

| Header          | Meaning                                  |
| --------------- | ---------------------------------------- |
| `Content-Type`  | Format of the request body (e.g., JSON)  |
| `Accept`        | What response format you expect          |
| `Authorization` | Your API key/token                       |
| `User-Agent`    | Info about the client making the request |

👉 **Example:**

```http
Content-Type: application/json
Authorization: Bearer abc123xyz
```

---

### 4. **Request Body – The Payload (for POST, PUT, PATCH)**

When you *send* data to the server (e.g., creating a user), this is where it goes.

🧾 **Example JSON Body:**

```json
{
  "name": "Bala",
  "email": "bala@example.com",
  "role": "Tester"
}
```

> 🔥 Pro Tip: For **GET** and **DELETE**, request body is usually **not used**.

---

### 5. **Query Parameters vs Path Parameters – Don’t Mix ‘Em Up**

* **Path Parameter**: Part of the endpoint
  → `/users/123` → `123` is a path param (specific user)

* **Query Parameter**: Optional filters after `?`
  → `/users?active=true&sort=desc`

---

## 📬 **API Response – The Reply from the Server**

Once you knock, the server answers. Here’s what you’ll get:

---

### 1. **HTTP Status Code – The Server’s Mood**

| Code | Meaning               | Explanation                       |
| ---- | --------------------- | --------------------------------- |
| 200  | OK                    | Request successful                |
| 201  | Created               | New resource created              |
| 204  | No Content            | Success, but no content returned  |
| 400  | Bad Request           | Something’s wrong in your request |
| 401  | Unauthorized          | Missing or invalid auth           |
| 403  | Forbidden             | You don’t have permission         |
| 404  | Not Found             | The resource doesn’t exist        |
| 500  | Internal Server Error | The backend messed up             |

---

### 2. **Response Body – The Data You Asked For**

Most often in **JSON**. Could be XML, plain text, etc.

👉 **Example JSON:**

```json
{
  "id": 123,
  "name": "Bala",
  "email": "bala@example.com"
}
```

---

### 3. **Response Headers – The Server's Envelope Info**

Same as request headers, but from the server side.

Common ones:

* `Content-Type: application/json`
* `Date: Thu, 09 May 2025 09:00:00 GMT`
* `Cache-Control: no-cache`
* `Server: nginx`

---

## 🧪 Summary Cheat Sheet

| Part        | Request                  | Response                  |
| ----------- | ------------------------ | ------------------------- |
| URL         | `https://.../resource`   | N/A                       |
| Method      | `GET`, `POST`, etc.      | N/A                       |
| Headers     | Content-Type, Auth, etc. | Content-Type, Cache, etc. |
| Body        | JSON/XML payload         | JSON/XML response         |
| Status Code | N/A                      | 200, 400, 500, etc.       |

---
# 🚦 Lesson 3: **HTTP Methods – CRUD Breakdown in Real-World APIs**

CRUD = **Create**, **Read**, **Update**, **Delete**
Each of these maps beautifully to an HTTP method:

| CRUD Operation | HTTP Method     | Purpose                        |
| -------------- | --------------- | ------------------------------ |
| **Create**     | `POST`          | Add new resource               |
| **Read**       | `GET`           | Retrieve resource              |
| **Update**     | `PUT` / `PATCH` | Modify resource (full/partial) |
| **Delete**     | `DELETE`        | Remove resource                |

We’ll use `https://reqres.in` again for practical examples.

---

## 🟢 1. `GET` – Read/Retrieve Data

### 🎯 Use:

* Fetch a user list or details
* No body required in the request

```java
@Test
public void getUser() {
    given()
        .when()
        .get("https://reqres.in/api/users/2")
        .then()
        .statusCode(200)
        .body("data.id", equalTo(2));
}
```

---

## 🟢 2. `POST` – Create New Data

### 🎯 Use:

* Create a new user, ticket, product, etc.
* Requires **payload** in request

```java
@Test
public void createUser() {
    String payload = """
        {
          "name": "Bala",
          "job": "Automation Tester"
        }
        """;

    given()
        .contentType(ContentType.JSON)
        .body(payload)
    .when()
        .post("https://reqres.in/api/users")
    .then()
        .statusCode(201)
        .body("name", equalTo("Bala"));
}
```

---

## 🟡 3. `PUT` – Full Update (Replace)

### 🎯 Use:

* Replace the **entire** resource
* Requires **complete payload** even if one field changes

```java
@Test
public void updateUser_PUT() {
    String payload = """
        {
          "name": "Bala",
          "job": "Lead QA"
        }
        """;

    given()
        .contentType(ContentType.JSON)
        .body(payload)
    .when()
        .put("https://reqres.in/api/users/2")
    .then()
        .statusCode(200)
        .body("job", equalTo("Lead QA"));
}
```

> 🧠 Think of `PUT` as **"replace this whole record"**.

---

## 🟡 4. `PATCH` – Partial Update

### 🎯 Use:

* Modify **part** of the resource (e.g., just update the job)

```java
@Test
public void updateUser_PATCH() {
    String payload = """
        {
          "job": "Principal QA"
        }
        """;

    given()
        .contentType(ContentType.JSON)
        .body(payload)
    .when()
        .patch("https://reqres.in/api/users/2")
    .then()
        .statusCode(200)
        .body("job", equalTo("Principal QA"));
}
```

> 🧠 Use `PATCH` when you only need to update one or two fields.

---

## 🔴 5. `DELETE` – Remove Resource

### 🎯 Use:

* Remove a resource (like a user or post)
* Usually returns **204 No Content**

```java
@Test
public void deleteUser() {
    given()
        .when()
        .delete("https://reqres.in/api/users/2")
        .then()
        .statusCode(204);
}
```

> 🧨 DELETE is permanent in most cases, no response body expected.

---

## 🧠 Summary Table – CRUD Mapping

| HTTP Method | CRUD Action      | Body Required? | Response Type     | Use When...               |
| ----------- | ---------------- | -------------- | ----------------- | ------------------------- |
| `GET`       | Read             | ❌              | 200 + data        | Fetching data             |
| `POST`      | Create           | ✅              | 201 + created obj | Creating a new record     |
| `PUT`       | Update (Full)    | ✅              | 200 + updated obj | Replacing entire resource |
| `PATCH`     | Update (Partial) | ✅              | 200 + updated obj | Updating specific fields  |
| `DELETE`    | Delete           | ❌              | 204               | Removing data             |

---

## 🎯 Your Hands-On Practice (Challenge Time!)

1. Create a new user with `POST`, then update it using `PUT` and `PATCH`.
2. Try deleting a user and validate `204`.
3. Play with sending invalid payloads and observe how status codes behave.

---
# 🔐 Lesson 4: **Authentication and Authorization**

| 🔑 **Authentication**              | 🚦 **Authorization**                |
| ---------------------------------- | ----------------------------------- |
| "Who are you?"                     | "What are you allowed to do?"       |
| Verifies identity                  | Grants or denies access rights      |
| Happens **first**                  | Happens **after** authentication    |
| E.g., Login with username/password | E.g., Only Admins can delete a user |
| Result: **You are verified**       | Result: **You are permitted**       |

> 🔁 **AuthN before AuthZ** – You can't be authorized if you're not authenticated.

---

## 🧩 **Types of Authentication**

Let’s get tactical. Here are the major types:

---

### 1. **Basic Authentication**

* 🔐 Uses: `username:password` encoded in **Base64**
* 🧾 Header:

  ```http
  Authorization: Basic dXNlcjpwYXNz
  ```
* 🧨 Downside: Not secure unless used with HTTPS

✅ Good for:

* Internal APIs
* Quick testing
* Legacy systems

---

### 2. **Bearer Token Authentication**

* 🔑 You get a **token** after login
* 📩 Token is sent with every request:

  ```http
  Authorization: Bearer eyJhbGciOiJIUz...
  ```
* Token is opaque — server stores or validates it

✅ Good for:

* Public REST APIs
* Session-less systems
* Token rotation supported

---

### 3. **JWT (JSON Web Token)**

* 🍱 Token contains user info (claims) and is **self-contained**
* 🔐 Verified via **signature**, no server-side storage needed
* Looks like this:

  ```
  Header.Payload.Signature
  ```
* Example Claims:

  * `sub`: user ID
  * `role`: admin/user
  * `exp`: expiry time

✅ Good for:

* Microservices
* Mobile apps
* Stateless auth

---

### 4. **OAuth 2.0**

* 🌐 Industry-standard for **secure delegated access**
* Used by: Google, Facebook, GitHub, etc.
* 🧭 Flow:

  1. App redirects to auth server
  2. User logs in and approves
  3. App gets **access token**
  4. Use token to call protected APIs

🛡️ Types of OAuth Grant Flows:

* **Authorization Code** (web apps)
* **Client Credentials** (machine-to-machine)
* **Password Grant** (deprecated but still seen)
* **Refresh Token** (get new access token)

✅ Good for:

* Third-party app integration
* Securing APIs for external users

---

### 5. **API Keys**

* 🔑 Key is a simple string like: `x-api-key: abc123`
* Often passed in headers or URL
* Not tied to user identity

✅ Good for:

* Rate-limiting
* Read-only public access
* Anonymous access tracking

---

### 6. **Digest Authentication** (rare now)

* Like Basic Auth, but hashes credentials using a challenge-response
* More secure than Basic, but rarely used today

---

### 7. **SAML / OpenID Connect**

* Enterprise-level protocols for **SSO (Single Sign-On)**
* Used in large orgs with **Active Directory** or **Identity Providers (IdP)**

✅ Good for:

* Enterprise login systems
* Federated identity

---

## 🧠 Summary Table

| Type         | Credential Type      | Secure? | Common Use Case                      |
| ------------ | -------------------- | ------- | ------------------------------------ |
| Basic Auth   | Username & Password  | ❌       | Legacy, internal apps                |
| Bearer Token | Token string         | ✅       | Modern REST APIs                     |
| JWT          | Token with payload   | ✅✅      | Stateless systems, microservices     |
| OAuth 2.0    | Token via login flow | ✅✅      | Third-party integrations, mobile/web |
| API Key      | Static key           | ⚠️      | Anonymous/limited access             |
| Digest       | Hashed credentials   | ✅       | Rarely used                          |
| SAML / OIDC  | Federated identity   | ✅✅      | Enterprise SSO                       |

---

## 🎤 Real Talk

* **Basic Auth** is like a house key under the mat — works, but risky.
* **JWT** is like carrying a passport — self-verified but risky if stolen.
* **OAuth 2.0** is like a valet card — lets others access, but only what you allow.
* **API Key** is like a guest pass — no identity, but limited access.

---

## 📚 What Should You Learn First?

If you're building automation tests for real-world APIs:

1. ✅ Basic Auth — easy to mock/test
2. ✅ Bearer Token + JWT — most common combo
3. 🔐 OAuth 2.0 — optional, but powerful for advanced APIs
 APIs**
---

# 📦 Lesson 7: Headers & Cookies in API Testing

Let’s crack open the box of metadata magic — where control, identity, and intent live in every API call.

---

### 🧾 **1. What Are Headers?**

**Headers** are key-value pairs sent along with every HTTP request and response.

They **describe** the request/response — like format, auth, caching, etc.
They don’t contain the main data (that’s in the body), but they control how the data is **processed**.

---

### ✉️ **Common Request Headers:**

| Header          | Purpose                             | Example                 |
| --------------- | ----------------------------------- | ----------------------- |
| `Content-Type`  | Tells server the format of the body | `application/json`      |
| `Accept`        | Tells server what format you expect | `application/json`      |
| `Authorization` | Sends token or credentials          | `Bearer <token>`        |
| `User-Agent`    | Info about client (browser/tool)    | `PostmanRuntime/7.29.2` |
| `Custom-Header` | Any business-defined custom header  | `X-App-Version: 3.2.1`  |

---

### 📩 Response Headers:

| Header           | Purpose                 | Example                              |
| ---------------- | ----------------------- | ------------------------------------ |
| `Content-Type`   | Format of the response  | `application/json`                   |
| `Set-Cookie`     | Sends cookies to client | `sessionId=abc123; Path=/; HttpOnly` |
| `Cache-Control`  | Caching rules           | `no-store, must-revalidate`          |
| `Content-Length` | Size of response body   | `2456`                               |
| `Server`         | Server info             | `nginx/1.18.0`                       |

---

### 🍪 **2. What Are Cookies in API?**

Cookies are **small pieces of data** stored on the client and sent with every request to the same server.
Mostly used for:

* **Sessions** (track logged-in users)
* **Preferences** (language, theme, etc.)

---

### 🛠 Headers vs Cookies:

| Feature      | Headers                      | Cookies                              |
| ------------ | ---------------------------- | ------------------------------------ |
| Sent by      | Client & Server              | Set by server, sent by client        |
| Use case     | Control info, metadata, auth | Session tracking, personalization    |
| Stored where | Not stored, transient        | Stored in browser or client          |
| Editable?    | Yes (in code or Postman)     | Yes, but typically managed by server |

---

### 🧪 How to Use in Rest Assured:

#### ✅ Sending Headers:

```java
given()
  .header("Content-Type", "application/json")
  .header("Authorization", "Bearer " + token)
```

#### ✅ Sending Cookies:

```java
given()
  .cookie("sessionId", "abc123")
```

#### ✅ Getting Headers/Cookies from Response:

```java
Response res = given().get("/api/users");

String contentType = res.getHeader("Content-Type");
Map<String, String> cookies = res.getCookies();
```

---

### 🔥 Why Headers & Cookies Matter in Testing:

* Test **API versioning**: `X-API-Version: v2`
* Validate **auth flow** via headers
* Check for **secure flags** in cookies: `HttpOnly`, `Secure`
* Simulate different clients or user roles
* Validate **CORS** behavior via headers

---

---
# 📌 **Glossay**

### 1. **What Is Metadata?**

**Metadata** = “Data about data.”

In the context of APIs, **metadata** is like the envelope around your message — it gives information *about* your request or response but isn’t the main data.

🧠 **Example (in Headers):**

```http
Content-Type: application/json
Authorization: Bearer abc123
```

This isn’t the actual **user data** you want — it’s just *info about how to handle* that data:

* `Content-Type` tells the server what format the request body is in.
* `Authorization` tells the server who you are.

> In short: **Headers** are the metadata of an API request/response.

---

### 2. **What Are Path Parameters?**

**Path Parameters** are part of the **URL path**. They’re used to **identify a specific resource**.

🧾 **Example:**

```http
GET https://api.example.com/users/123
```

* `/users` → Collection
* `/123` → Path parameter (specific user with ID 123)

> 💡 Think of this like a file path on your computer.

---

### 3. **What Are Query Parameters?**

**Query Parameters** are **filters or options** you pass in the URL, *after the `?`* symbol. They're used to **narrow down the results** or tweak the request.

🧾 **Example:**

```http
GET https://api.example.com/users?role=admin&active=true
```

* `?role=admin&active=true` are query parameters
* You’re asking for users where:

  * `role` is `admin`
  * `active` is `true`

> 💡 Think of this like searching in Excel using filters.

---

### 🎯 **Path vs Query Parameters – What's the Difference?**

| Feature   | Path Parameter                       | Query Parameter                        |
| --------- | ------------------------------------ | -------------------------------------- |
| Position  | In the URL path (e.g., `/users/123`) | After the `?` (e.g., `?active=true`)   |
| Purpose   | Identify a specific resource         | Filter, sort, or customize the request |
| Required? | Usually **mandatory**                | Usually **optional**                   |
| Use Case  | `/products/456` → Product 456        | `/products?category=books&sort=price`  |

---

### 4. **What Is a Payload?**

**Payload** = The **main data** you're sending in a request.

Only used in methods like:

* `POST` (create)
* `PUT` / `PATCH` (update)

🧾 **Example Payload (JSON body):**

```json
{
  "name": "Bala",
  "email": "bala@example.com"
}
```

> 💡 The payload is the *content* of your message — like the “meat” in your sandwich.

---

### 5. **What Is an Endpoint?**

**Endpoint** = The specific **URL** where the API service is listening.

🧾 **Example:**

```http
https://api.example.com/users
```

* This is an endpoint that handles `/users` related operations.
* Think of an endpoint as the **door you knock on** to talk to a system.

Each operation (GET, POST, etc.) might go to the same endpoint, but with different behaviors.

---

## **6. What is JSON Path?**

**JSONPath** is like XPath, but for JSON.

It’s a **query language** for navigating through and extracting parts of a JSON structure. Think of it like giving directions inside a JSON document.

### Example JSON:

```json
{
  "user": {
    "id": 101,
    "name": "Bala",
    "skills": ["Java", "Selenium", "API"]
  }
}
```

### JSONPath Examples:

| JSONPath             | What it fetches          | Result   |
| -------------------- | ------------------------ | -------- |
| `user.id`            | User ID                  | `101`    |
| `user.name`          | Name                     | `"Bala"` |
| `user.skills[0]`     | First skill in the array | `"Java"` |
| `user.skills.size()` | Size of the skills array | `3`      |

> 💡 JSONPath is case-sensitive and dot-based (e.g., `data.id`, `user.name`).

---

## **7. What is a Schema?**

A **schema** defines the expected **structure** of your JSON (or XML) response — like a blueprint or contract.

### In JSON, it’s written in **JSON Schema format** and it:

* Specifies data types (e.g., string, number, boolean)
* Defines required fields
* Sets constraints (like min/max, enum values)
* Helps you validate *structure*, not just values

### Example Schema for JSON:

```json
{
  "type": "object",
  "properties": {
    "user": {
      "type": "object",
      "properties": {
        "id": { "type": "integer" },
        "name": { "type": "string" }
      },
      "required": ["id", "name"]
    }
  }
}
```

> In Rest Assured, you can use `matchesJsonSchemaInClasspath("schema.json")` to validate response against a schema.

---

## **8.What is a Matcher?**

A **Matcher** is a condition that checks whether a value **meets expectations**.

Rest Assured uses the **Hamcrest Matcher Library** to validate responses.

---

## 🎯 **Common Matchers in Rest Assured (via Hamcrest)**

| Matcher                 | Description                              | Example                                     |
| ----------------------- | ---------------------------------------- | ------------------------------------------- |
| `equalTo(value)`        | Checks if value is exactly equal         | `equalTo("Bala")`                           |
| `containsString(value)` | Checks if value contains substring       | `containsString("@gmail.com")`              |
| `hasItem(value)`        | Checks if array contains a value         | `hasItem("API")`                            |
| `hasItems(val1, val2)`  | Checks if array contains multiple values | `hasItems("Java", "API")`                   |
| `not(value)`            | Negates a condition                      | `not(equalTo("Error"))`                     |
| `nullValue()`           | Checks if value is null                  | `nullValue()`                               |
| `notNullValue()`        | Checks if value is not null              | `notNullValue()`                            |
| `startsWith(prefix)`    | Checks if string starts with a value     | `startsWith("Bal")`                         |
| `endsWith(suffix)`      | Checks if string ends with a value       | `endsWith(".in")`                           |
| `anyOf(m1, m2)`         | Logical OR between multiple matchers     | `anyOf(equalTo("Bala"), equalTo("Tester"))` |
| `allOf(m1, m2)`         | Logical AND between multiple matchers    | `allOf(startsWith("Ba"), endsWith("a"))`    |

> These matchers make your tests readable, flexible, and expressive.

---

## 🧪 Example Usage in Rest Assured:

```java
given()
    .when()
    .get("/users/1")
    .then()
    .body("user.name", equalTo("Bala"))
    .body("user.email", containsString("@gmail.com"))
    .body("user.skills", hasItem("API"))
    .body("user.id", notNullValue());
```

### 9. **What is API Mocking?**

**API Mocking** is the process of **creating fake endpoints** that return **predefined responses**. These mocked endpoints behave *as if* they were real, without hitting the actual backend server.

Think of it as **"fake it till you build it."**

---

### 💼 **Why Mock APIs?**

| Scenario                                  | Why Mocking Helps                                     |
| ----------------------------------------- | ----------------------------------------------------- |
| Backend isn't ready                       | Frontend or test teams can start work without waiting |
| Third-party APIs are rate-limited or paid | Avoid cost & throttling                               |
| Unstable or flaky APIs                    | Get consistent responses for testing                  |
| Test specific edge cases                  | Force 500 errors, timeouts, invalid data              |
| Speed up testing                          | Mocks are faster and isolated                         |

---

### 🛠️ **What Can You Mock?**

* **HTTP methods** (GET, POST, etc.)
* **Response bodies** (JSON/XML)
* **Status codes** (200, 404, 500)
* **Response delays** (simulate latency)
* **Headers/Cookies**
* **Conditional logic** (e.g., mock response based on request payload)

---

### 🔧 **Popular Tools for API Mocking**

| Tool                          | Description                                                    |
| ----------------------------- | -------------------------------------------------------------- |
| **WireMock**                  | Java-based, super powerful for mocking HTTP APIs               |
| **MockServer**                | Also Java-based, can be run standalone or embedded             |
| **Postman Mock Server**       | Cloud-hosted, great for teams already using Postman            |
| **Beeceptor**                 | Online API mocking, super simple setup                         |
| **JSON Server**               | Turns a JSON file into a full mock REST API in seconds         |
| **MSW (Mock Service Worker)** | Frontend-focused mocking, intercepts requests at browser level |

---

### 📦 **Example – WireMock**

You can create a mock for `GET /user/101` that returns:

```json
{
  "id": 101,
  "name": "Bala",
  "role": "Tester"
}
```

Even if the real `/user/101` API isn’t built yet.

---

### 🧪 **When Testing with Mocks Makes Sense**

* Unit tests (where you isolate code logic)
* API automation before the backend is complete
* Regression testing with predictable results
* Simulating edge cases (timeouts, invalid responses)

---

### ⚠️ **Caution: Don't Overuse It**

Mocking is great, but **real integration** is still king. Always validate against the actual API **before go-live** to catch environment or data inconsistencies.

---

### 🎯 In Short:

> Mocking APIs is the art of **pretending with purpose** — it lets you simulate real-world API behavior so teams can move fast without waiting on others.

---
## 10.️ What is `RequestSpecBuilder`?

It's a **builder class** used to define a common template for all your HTTP requests.

> Think of it as your **request blueprint** — headers, base URIs, auth, and more — all preconfigured.

### 🔧 Why use it?

Without it:
```java
given()
   .baseUri("https://api.example.com")
   .header("Authorization", "Bearer token123")
   .contentType(ContentType.JSON)
   .body(jsonPayload)
.when()
   .post("/users");
```

With `RequestSpecBuilder`:
```java
RequestSpecification reqSpec = new RequestSpecBuilder()
   .setBaseUri("https://api.example.com")
   .addHeader("Authorization", "Bearer token123")
   .setContentType(ContentType.JSON)
   .build();

given()
   .spec(reqSpec)
   .body(jsonPayload)
.when()
   .post("/users");
```

> 🔁 You now reuse `reqSpec` across multiple tests. DRY principle ✅

---

## 11. What is `ResponseSpecBuilder`?

This creates a **common expectation** or set of **assertions** for your API responses.

> It’s your **response contract** — status code, content type, headers, or body structure can be validated once and reused.

### 🎯 Why use it?

Without it:
```java
.then()
   .statusCode(200)
   .contentType(ContentType.JSON);
```

With `ResponseSpecBuilder`:
```java
ResponseSpecification resSpec = new ResponseSpecBuilder()
   .expectStatusCode(200)
   .expectContentType(ContentType.JSON)
   .build();

given()
   .spec(reqSpec)
.when()
   .get("/users")
.then()
   .spec(resSpec);
```

---

## 🧩 Combine both for a solid testing foundation:

```java
RequestSpecification reqSpec = new RequestSpecBuilder()
   .setBaseUri("https://api.example.com")
   .addHeader("Authorization", "Bearer abc123")
   .setContentType(ContentType.JSON)
   .build();

ResponseSpecification resSpec = new ResponseSpecBuilder()
   .expectStatusCode(200)
   .expectContentType(ContentType.JSON)
   .build();

given()
   .spec(reqSpec)
.when()
   .get("/users")
.then()
   .spec(resSpec);
```

---

## 💼 Real-World Use Cases

- You’re calling multiple APIs with the same base URI and headers — use `RequestSpecBuilder`.
- You expect all your successful GET requests to return 200 and JSON — use `ResponseSpecBuilder`.
- Want clean, readable test methods — combine both and offload boilerplate.

---

### 🔚 Summary

| Term            | Meaning                                         | Example                              |
| --------------- | ----------------------------------------------- | ------------------------------------ |
| **Metadata**    | Data *about* the request/response (via headers) | `Content-Type: application/json`     |
| **Path Param**  | Specific ID in the URL path                     | `/users/123` → User with ID 123      |
| **Query Param** | Filters or modifiers after `?` in the URL       | `?active=true&sort=asc`              |
| **Payload**     | Data sent in the body (for POST/PUT)            | `{ "name": "Bala", "email": "..." }` |
| **Endpoint**    | The full URL you hit to call an API             | `https://api.example.com/users`      |
| **JSONPath**    | Way to access and extract specific JSON fields     |                                      |
| **Schema**      | Blueprint defining structure and data types        |                                      |
| **Matcher**     | A condition or rule for validating API response data   |                                      |
|**Mocking APIs**     |It lets you simulate real-world API behavior so teams can move fast without waiting on others| |
| **RequestSpecBuilder** | Define a **standard request template**         |
| **ResponseSpecBuilder**| Define a **standard response expectation**     |
---
