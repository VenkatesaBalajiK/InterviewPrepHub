# 📘 API Testing Hands-On Notes — Index

* [Introduction to Rest Assured](#lesson-1-using-rest-assured--time-to-write-some-real-code-and-test-like-a-boss-)
* [🧪 Headers & Cookies in Rest Assured](#-hands-on-headers-cookies-in-rest-assured)
     * [✅ 1. **Sending Headers in a Request**](#-1-sending-headers-in-a-request)
     * [✅ 2. **Getting Response Headers**](#-2-getting-response-headers)
     * [✅ 3. **Sending Cookies in a Request**](#-3-sending-cookies-in-a-request)
     * [✅ 4. **Extracting Cookies from Response**](#-4-extracting-cookies-from-response)
* [🧱 Custom Header Generator Utility Class](#-custom-header-generator-utility-class-headerutiljava)
* [🔐 Token & Cookie Utility Classes](#-token-cookie-utility-classes)
* [🔧 RequestSpecBuilder — Your Master Configurator](#-requestspecbuilderjava-your-master-configurator)
* [🧪 Response Validation in Rest Assured](#-hands-on-response-validation-in-rest-assured)
     * [✅ 1. **Status Code Validation**](#-1-status-code-validation)
     * [✅ 2. **Response Body Validation (JSON)**](#-2-response-body-validation-json)
     * [✅ 3. **Header Validation**](#-3-header-validation)
     * [✅ 4. **Cookie Validation**](#-4-cookie-validation)
     * [✅ 5. **Schema Validation**](#-5-schema-validation)
* [**JSON Response Validation**](#1-json-response-validation)
* [**XML Response Validation**](#2-xml-response-validation)
* [**Advanced Concepts in Response Validation**](#3-advanced-concepts-in-response-validation)
   * [1. **Schema Validation (JSON)**](#1-schema-validation-json)
   * [2. **Dynamic Validation (Using Variables)**](#2-dynamic-validation-using-variables)

---
## **Lesson 1** using **Rest Assured** — time to write some real code and *test like a boss*. 🔥

---

## 🔧 **Setup – Rest Assured + TestNG (or JUnit)**

### ✅ Prerequisites:

* **Java project** (Maven-based preferred)
* **IDE**: IntelliJ or Eclipse
* **Add dependencies** in `pom.xml`:

```xml
<dependencies>
    <!-- Rest Assured -->
    <dependency>
        <groupId>io.rest-assured</groupId>
        <artifactId>rest-assured</artifactId>
        <version>5.4.0</version>
        <scope>test</scope>
    </dependency>

    <!-- TestNG (optional) -->
    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng</artifactId>
        <version>7.8.0</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

---

## 🚀 **Hands-on Code – Hitting a Real API (GET Request)**

We'll use a public test API:
🔗 `https://reqres.in/api/users/2` – returns details of a user.

```java
import io.restassured.RestAssured;
import io.restassured.response.Response;
import org.testng.annotations.Test;

import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class GetUserAPITest {

    @Test
    public void getUserDetails() {
        // Base URI
        RestAssured.baseURI = "https://reqres.in";

        // Hitting the endpoint with GET
        Response response = given()
                                .header("Content-Type", "application/json")  // Metadata
                            .when()
                                .get("/api/users/2")  // Endpoint + Path Param
                            .then()
                                .statusCode(200)       // Validate Status Code
                                .body("data.id", equalTo(2))  // Payload check
                                .body("data.first_name", equalTo("Janet"))
                                .extract().response();

        // Logging full response (for manual inspection)
        System.out.println("Response Body:\n" + response.getBody().asPrettyString());
    }
}
```

---

## 🧠 **What You Just Did (Mapped to Lesson 1)**

| Concept                | Your Code                                    |
| ---------------------- | -------------------------------------------- |
| **Endpoint**           | `https://reqres.in/api/users/2`              |
| **Path Param**         | `/2` → user ID                               |
| **Method**             | `get()`                                      |
| **Metadata/Header**    | `"Content-Type", "application/json"`         |
| **Payload (Response)** | Validated fields from JSON (`data.id`, etc.) |
| **Status Code**        | `statusCode(200)`                            |

---

## 📦 Output Sample

```json
{
  "data": {
    "id": 2,
    "email": "janet.weaver@reqres.in",
    "first_name": "Janet",
    "last_name": "Weaver",
    ...
  }
}
```

---

## 🛠 Your Assignment (Optional, but boss move if you do it):

1. Change the user ID to `23` (non-existent user) and observe the status code.
2. Add a query parameter (`?delay=3`) and try `.get("/api/users?delay=3")`.
3. Log headers from the response using:

```java
System.out.println("Headers:\n" + response.getHeaders());
```

---

## 🧪 Hands-On: Headers & Cookies in Rest Assured

### 🔧 Step 1: Setup

Make sure you have:

```xml
<dependency>
  <groupId>io.rest-assured</groupId>
  <artifactId>rest-assured</artifactId>
  <version>5.3.0</version>
  <scope>test</scope>
</dependency>
```

---

### ✅ 1. **Sending Headers in a Request**

```java
import io.restassured.RestAssured;
import static io.restassured.RestAssured.*;

public class HeaderTest {

    public static void main(String[] args) {
        RestAssured.baseURI = "https://reqres.in";

        given()
            .header("Content-Type", "application/json")
            .header("Custom-Header", "Bala-Test-Run")
        .when()
            .get("/api/users/2")
        .then()
            .statusCode(200)
            .log().headers();
    }
}
```

---

### ✅ 2. **Getting Response Headers**

```java
import io.restassured.response.Response;

public class GetResponseHeaders {

    public static void main(String[] args) {
        Response res = given()
                            .when()
                            .get("https://reqres.in/api/users/2");

        System.out.println("Content-Type: " + res.getHeader("Content-Type"));
        System.out.println("All Headers:\n" + res.getHeaders());
    }
}
```

---

### ✅ 3. **Sending Cookies in a Request**

```java
public class SendCookieTest {

    public static void main(String[] args) {
        given()
            .cookie("sessionId", "abc123")
        .when()
            .get("https://httpbin.org/cookies")
        .then()
            .statusCode(200)
            .log().body(); // httpbin echoes the cookies sent
    }
}
```

---

### ✅ 4. **Extracting Cookies from Response**

```java
public class ReadCookiesTest {

    public static void main(String[] args) {
        Response response = get("https://httpbin.org/cookies/set/sessionId/xyz789");

        System.out.println("All Cookies: " + response.getCookies());
        System.out.println("sessionId: " + response.getCookie("sessionId"));
    }
}
```

---

### 🧠 Pro Tips:

* Use `multiValueHeaders()` for headers that appear multiple times
* `headers()` accepts multiple `.header()` calls in one chain
* Use `cookies()` to set multiple cookies at once
* Use `log().headers()` or `log().cookies()` to debug like a pro

---
## 🧱 Custom Header Generator Utility Class – `HeaderUtil.java`
Building a **Custom Header Generator** is a slick way to keep your code clean, dynamic, and DRY (Don’t Repeat Yourself). Especially when your headers involve tokens, API keys, or custom values.

Let’s build it like a pro — scalable, reusable, test-ready.

---
### ✅ Goal:

Centralize and manage headers for all requests — static, dynamic, authenticated, custom.

---

### 📂 Project Structure:

```
src/
 └── utils/
     └── HeaderUtil.java
 └── tests/
     └── YourRestAssuredTest.java
```
---

### 🔧 `HeaderUtil.java`

```java
package utils;

import io.restassured.http.Header;
import io.restassured.http.Headers;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;

public class HeaderUtil {

    // Common headers for all requests
    public static Headers getDefaultHeaders() {
        List<Header> headers = new ArrayList<>();
        headers.add(new Header("Content-Type", "application/json"));
        headers.add(new Header("Accept", "application/json"));
        return new Headers(headers);
    }

    // Authenticated headers (Bearer Token)
    public static Headers getAuthHeaders(String token) {
        List<Header> headers = new ArrayList<>();
        headers.add(new Header("Content-Type", "application/json"));
        headers.add(new Header("Authorization", "Bearer " + token));
        return new Headers(headers);
    }

    // Custom header generator from Map (for test data-driven or external headers)
    public static Headers getHeadersFromMap(Map<String, String> headerMap) {
        List<Header> headers = new ArrayList<>();
        for (Map.Entry<String, String> entry : headerMap.entrySet()) {
            headers.add(new Header(entry.getKey(), entry.getValue()));
        }
        return new Headers(headers);
    }
}
```

---

### 🧪 Example Usage in Your Test

```java
import static io.restassured.RestAssured.*;
import io.restassured.response.Response;
import utils.HeaderUtil;
import java.util.HashMap;
import java.util.Map;

public class YourRestAssuredTest {

    public static void main(String[] args) {

        // Using default headers
        given()
            .headers(HeaderUtil.getDefaultHeaders())
        .when()
            .get("https://reqres.in/api/users/2")
        .then()
            .statusCode(200);

        // Using auth headers
        String dummyToken = "test123token";
        Response res = given()
            .headers(HeaderUtil.getAuthHeaders(dummyToken))
        .when()
            .get("https://reqres.in/api/users/2");

        // Using custom map-based headers
        Map<String, String> customHeaders = new HashMap<>();
        customHeaders.put("X-App-Version", "3.5.7");
        customHeaders.put("X-User-Type", "Admin");

        given()
            .headers(HeaderUtil.getHeadersFromMap(customHeaders))
        .when()
            .get("https://reqres.in/api/users/2")
        .then()
            .statusCode(200);
    }
}
```

---

### 🔥 Why This Rocks:

* You centralize header logic (makes test methods cleaner)
* Easy to plug into a framework later
* Works with both hardcoded and dynamic tokens/custom values
* Map support = data-driven testing friendly

---

## 🔐 Token & Cookie Utility Classes

### 📦 1. `TokenUtil.java`

For generating or injecting tokens dynamically or statically.

### 🍪 2. `CookieUtil.java`

To manage custom cookies, session simulation, or cookie-based auth.

---

## ✅ 1. `TokenUtil.java`

You can scale this to pull tokens from login APIs, config files, or env vars.

```java
package utils;

public class TokenUtil {

    // Dummy static token (replace with actual login call later)
    public static String getStaticToken() {
        return "your-static-token-goes-here";
    }

    // Dynamic token from login API (example structure)
    /*
    public static String getAuthToken() {
        Response response = given()
                                .body("{\"username\": \"admin\", \"password\": \"password\"}")
                                .contentType("application/json")
                            .when()
                                .post("https://yourapi.com/login");

        return response.jsonPath().getString("token");
    }
    */
}
```

---

## 🍪 2. `CookieUtil.java`

```java
package utils;

import io.restassured.http.Cookie;
import io.restassured.http.Cookies;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;

public class CookieUtil {

    // Single session cookie
    public static Cookies getSessionCookie(String sessionId) {
        Cookie cookie = new Cookie.Builder("sessionId", sessionId).build();
        return new Cookies(cookie);
    }

    // Custom cookies from a map (for data-driven tests)
    public static Cookies getCookiesFromMap(Map<String, String> cookieMap) {
        List<Cookie> cookieList = new ArrayList<>();
        for (Map.Entry<String, String> entry : cookieMap.entrySet()) {
            cookieList.add(new Cookie.Builder(entry.getKey(), entry.getValue()).build());
        }
        return new Cookies(cookieList);
    }
}
```

---

## 🧪 Example Usage in a Test Class

```java
import static io.restassured.RestAssured.*;
import utils.HeaderUtil;
import utils.TokenUtil;
import utils.CookieUtil;
import java.util.Map;
import java.util.HashMap;

public class AuthCookieTest {

    public static void main(String[] args) {
        String token = TokenUtil.getStaticToken();

        // Cookie from session ID
        given()
            .headers(HeaderUtil.getAuthHeaders(token))
            .cookies(CookieUtil.getSessionCookie("abc123xyz"))
        .when()
            .get("https://httpbin.org/cookies")
        .then()
            .statusCode(200)
            .log().body();

        // Multiple cookies from Map
        Map<String, String> cookies = new HashMap<>();
        cookies.put("lang", "en");
        cookies.put("theme", "dark");

        given()
            .headers(HeaderUtil.getDefaultHeaders())
            .cookies(CookieUtil.getCookiesFromMap(cookies))
        .when()
            .get("https://httpbin.org/cookies")
        .then()
            .statusCode(200)
            .log().body();
    }
}
```

---

## 🔥 Advantages of Utility Classes:

| Benefit        | Description                                               |
| -------------- | --------------------------------------------------------- |
| 🔄 Reusability | Use same logic across test classes/frameworks             |
| 🧼 Clean Code  | Keeps test methods readable and focused                   |
| 🔐 Flexibility | Easy switch from static token → dynamic auth flow         |
| 🧪 Testability | Isolate and unit test cookie/token behavior independently |

---
## 🔧 `RequestSpecBuilder.java` — Your Master Configurator

We’ll create a utility that combines headers, cookies, and tokens into one **request specification**.

Let’s supercharge your framework, Bala! 🚀 The **RequestSpecBuilder** is a sleek way to consolidate and reuse all your request configurations (headers, cookies, tokens, etc.). Think of it as your **configurator** for every API call.

This utility will allow you to build your **request specification** with all needed customizations (like headers, cookies, authentication) and reuse it across multiple tests.

---

### 📂 Project Structure:

```
src/
 └── utils/
     └── HeaderUtil.java
     └── TokenUtil.java
     └── CookieUtil.java
     └── RequestSpecBuilder.java
 └── tests/
     └── YourRestAssuredTest.java
```

---

### 🛠 `RequestSpecBuilder.java`

```java
package utils;

import io.restassured.specification.RequestSpecification;
import io.restassured.http.Headers;
import io.restassured.http.Cookies;
import static io.restassured.RestAssured.*;

public class RequestSpecBuilder {

    // Default request spec with headers and basic setup
    public static RequestSpecification buildDefaultSpec() {
        return given()
            .headers(HeaderUtil.getDefaultHeaders())
            .log().all(); // log everything for visibility
    }

    // Request spec with Authorization (Bearer Token)
    public static RequestSpecification buildAuthSpec(String token) {
        return given()
            .headers(HeaderUtil.getAuthHeaders(token))
            .log().all();
    }

    // Request spec with cookies (session management)
    public static RequestSpecification buildWithCookies(Cookies cookies) {
        return given()
            .cookies(cookies)
            .log().all();
    }

    // Request spec with both headers and cookies
    public static RequestSpecification buildWithHeadersAndCookies(String token, Cookies cookies) {
        return given()
            .headers(HeaderUtil.getAuthHeaders(token))
            .cookies(cookies)
            .log().all();
    }

    // Request spec with custom headers from a Map
    public static RequestSpecification buildCustomSpec(Map<String, String> headerMap) {
        return given()
            .headers(HeaderUtil.getHeadersFromMap(headerMap))
            .log().all();
    }
}
```

---

### 🧪 Example Usage in Your Test

```java
import static io.restassured.RestAssured.*;
import utils.HeaderUtil;
import utils.TokenUtil;
import utils.CookieUtil;
import utils.RequestSpecBuilder;
import io.restassured.response.Response;
import io.restassured.http.Cookies;
import java.util.HashMap;
import java.util.Map;

public class ApiTest {

    public static void main(String[] args) {
        String token = TokenUtil.getStaticToken();
        
        // 1. Default spec
        Response res1 = RequestSpecBuilder.buildDefaultSpec()
                                          .when()
                                          .get("https://reqres.in/api/users/2");
        res1.then().statusCode(200);

        // 2. Auth spec with token
        Response res2 = RequestSpecBuilder.buildAuthSpec(token)
                                          .when()
                                          .get("https://reqres.in/api/users/2");
        res2.then().statusCode(200);

        // 3. Spec with cookies
        Cookies cookies = CookieUtil.getSessionCookie("abc123xyz");
        Response res3 = RequestSpecBuilder.buildWithCookies(cookies)
                                          .when()
                                          .get("https://httpbin.org/cookies");
        res3.then().statusCode(200).log().body();

        // 4. Spec with both headers and cookies
        Response res4 = RequestSpecBuilder.buildWithHeadersAndCookies(token, cookies)
                                          .when()
                                          .get("https://httpbin.org/cookies");
        res4.then().statusCode(200).log().body();

        // 5. Custom headers spec from Map
        Map<String, String> customHeaders = new HashMap<>();
        customHeaders.put("X-Custom-Header", "BalaTest");
        Response res5 = RequestSpecBuilder.buildCustomSpec(customHeaders)
                                          .when()
                                          .get("https://reqres.in/api/users/2");
        res5.then().statusCode(200);
    }
}
```

---

### 🔥 Why This Rocks:

* **Centralized Configuration**: All header, cookie, and token logic lives in one place.
* **Scalable**: Easy to extend with additional configurations (like authentication types).
* **Reusable**: Reuse specs across tests without repeating code.
* **Maintainable**: Changes to headers/cookies/tokens need to be updated in one place.

---

### 🔑 Key Points:

1. **Modularity**: You can easily modify your request specification in one class, and all your tests will reflect that change.
2. **Clarity**: Tests are more focused on the logic, not the configuration of headers and cookies.
3. **Dynamic Adaptability**: The builder can adapt dynamically to different test scenarios (with/without token, with cookies, custom headers, etc.).

---

### 🧪 Hands-On: Response Validation in Rest Assured

Validating the response is the final and crucial part of API testing. You’ll want to ensure that the API behaves as expected, checks the status codes, body content, headers, cookies, and more.

### 🔍 Key Areas of Response Validation:

1. **Status Code Validation**
2. **Response Body Validation (JSON/XML/Plain Text)**
3. **Header Validation**
4. **Cookie Validation**
5. **Schema Validation**

Let’s break these down with some hands-on examples using **Rest Assured**.

---

### ✅ 1. **Status Code Validation**

Status codes tell you how the API responded. You can validate it in several ways.

```java
import static io.restassured.RestAssured.*;

public class StatusCodeValidation {

    public static void main(String[] args) {
        given()
            .when()
            .get("https://reqres.in/api/users/2")
            .then()
            .statusCode(200); // Validates that the status code is 200 OK
    }
}
```

You can also check for `4xx` or `5xx` ranges dynamically:

```java
.then()
    .statusCode(anyOf(equalTo(200), equalTo(201))); // Check multiple status codes
```

---

### ✅ 2. **Response Body Validation (JSON)**

Validate the JSON body by checking specific keys and values.

```java
import static io.restassured.RestAssured.*;

public class BodyValidation {

    public static void main(String[] args) {
        given()
            .when()
            .get("https://reqres.in/api/users/2")
            .then()
            .body("data.id", equalTo(2)) // Validate 'data.id' in the response body
            .body("data.first_name", equalTo("Janet")) // Validate 'data.first_name'
            .body("data.email", containsString("janet@reqres.in")); // Validate substring match in 'data.email'
    }
}
```

You can use **Matchers** like:

* `equalTo()`
* `containsString()`
* `hasItems()`
* `notNullValue()`

---

### ✅ 3. **Header Validation**

You can validate response headers to check if specific headers are returned.

```java
import static io.restassured.RestAssured.*;

public class HeaderValidation {

    public static void main(String[] args) {
        given()
            .when()
            .get("https://reqres.in/api/users/2")
            .then()
            .header("Content-Type", equalTo("application/json; charset=utf-8")) // Validate header value
            .header("Server", notNullValue()); // Validate header presence
    }
}
```

You can also check if a header is present using:

```java
.then()
    .header("Content-Type", notNullValue());
```

---

### ✅ 4. **Cookie Validation**

You can validate cookies similarly to how we validated headers.

```java
import static io.restassured.RestAssured.*;

public class CookieValidation {

    public static void main(String[] args) {
        given()
            .when()
            .get("https://httpbin.org/cookies/set/sessionId/abc123")
            .then()
            .cookie("sessionId", equalTo("abc123")); // Validate cookie value
    }
}
```

---

### ✅ 5. **Schema Validation**

For complex APIs, you can validate the response structure using **JSON Schema**. This ensures that the response matches the predefined structure.

Here’s an example of how you’d validate a response against a schema:

1. **Define a JSON Schema** file (let’s call it `user-schema.json`).

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "data": {
      "type": "object",
      "properties": {
        "id": { "type": "integer" },
        "first_name": { "type": "string" },
        "last_name": { "type": "string" }
      },
      "required": ["id", "first_name"]
    }
  },
  "required": ["data"]
}
```

2. **Validate the response using Rest Assured**:

```java
import static io.restassured.RestAssured.*;

public class SchemaValidation {

    public static void main(String[] args) {
        given()
            .when()
            .get("https://reqres.in/api/users/2")
            .then()
            .assertThat()
            .body(matchesJsonSchemaInClasspath("user-schema.json"));
    }
}
```

This will ensure that the response body matches the schema in your `user-schema.json` file.

---

## 🧠 Pro Tips for Response Validation:

1. **Chaining Matchers**: Combine multiple validations in one line.

   ```java
   .then()
       .statusCode(200)
       .body("data.id", equalTo(2))
       .header("Content-Type", containsString("json"));
   ```

2. **Negative Assertions**: Use `not()` for negative testing.

   ```java
   .then()
       .body("data.email", not(equalTo("wrong-email@example.com")));
   ```

3. **JSON Path**: You can use JSON Path to traverse nested JSON objects or arrays.

   ```java
   .body("data.address.city", equalTo("New York"));
   ```

4. **Custom Matchers**: Build your own matchers if the default ones don’t suit your needs.

---

### 🔥 Wrapping Up:

* **Status Codes**: Check if the API response is valid (200, 404, etc.).
* **Body Content**: Validate if the returned data matches your expectations.
* **Headers and Cookies**: Ensure headers and cookies are correct and present.
* **Schema Validation**: Validate the response's structure against a schema (especially for large responses).

---

### 🧪 **Complex Response Validation**

In real-world testing, you’ll deal with APIs returning responses in **JSON** and **XML**. You’ll need to validate both their structure and their content effectively.

---

## 1. **JSON Response Validation**

### JSON Response Structure

JSON responses are often nested, so validating them involves checking specific paths to ensure data is returned as expected. Rest Assured provides powerful support for traversing and validating these JSON structures.

### 🧰 Example of JSON Response Validation

Let’s assume the response from an API is something like:

```json
{
  "data": {
    "id": 2,
    "first_name": "Janet",
    "last_name": "Weaver",
    "email": "janet@reqres.in",
    "address": {
      "street": "123 Elm St",
      "city": "New York"
    }
  }
}
```

### Validation Steps:

```java
import static io.restassured.RestAssured.*;

public class JsonValidationExample {

    public static void main(String[] args) {
        given()
            .when()
            .get("https://reqres.in/api/users/2")
            .then()
            .statusCode(200) // Validate status code
            .body("data.id", equalTo(2)) // Validate 'id' in the response body
            .body("data.first_name", equalTo("Janet")) // Validate 'first_name'
            .body("data.address.city", equalTo("New York")) // Validate nested field 'city'
            .body("data.email", containsString("reqres.in")) // Partial match on email
            .body("data.address.street", equalTo("123 Elm St")); // Validate nested field 'street'
    }
}
```

### 🧠 Concepts:

* **Dot notation** is used for accessing nested fields.
* You can use **matchers** like `equalTo()`, `containsString()`, `notNullValue()`, etc.

### JSON Path Syntax:

* **Single value**: `data.id`
* **Nested field**: `data.address.city`
* **Array**: `data.address[0].city` (for array elements)
* **Array size**: `data.address.size()` (to check array size)
* **Partial match**: `data.email`, `containsString("reqres.in")`

---

## 2. **XML Response Validation**

Many APIs still return XML responses, especially in legacy systems or SOAP APIs. XML responses have a tree structure, and validating them requires navigating through XML nodes.

### 🧰 Example of XML Response Validation

Let’s assume the XML response from an API looks like this:

```xml
<response>
    <status>200</status>
    <user>
        <id>2</id>
        <first_name>Janet</first_name>
        <last_name>Weaver</last_name>
        <email>janet@reqres.in</email>
        <address>
            <street>123 Elm St</street>
            <city>New York</city>
        </address>
    </user>
</response>
```

### Validation Steps:

```java
import static io.restassured.RestAssured.*;

public class XmlValidationExample {

    public static void main(String[] args) {
        given()
            .when()
            .get("https://reqres.in/api/users/2") // Assume XML response from this endpoint
            .then()
            .statusCode(200) // Validate status code
            .body("response.user.id", equalTo("2")) // Validate 'id' in XML
            .body("response.user.first_name", equalTo("Janet")) // Validate 'first_name'
            .body("response.user.address.city", equalTo("New York")) // Validate nested field 'city'
            .body("response.user.email", containsString("reqres.in")) // Partial match on email
            .body("response.user.address.street", equalTo("123 Elm St")); // Validate nested field 'street'
    }
}
```

### 🧠 Concepts:

* **XPath** is used to navigate XML elements.
* The XML body is traversed in a similar fashion to JSON using path expressions.

### XPath Syntax:

* **Single value**: `response.user.id`
* **Nested field**: `response.user.address.city`
* **Array**: `response.user.address[0].city`
* **Partial match**: `response.user.email`, `containsString("reqres.in")`

---

## 3. **Advanced Concepts in Response Validation**

Now, let’s go a step further by incorporating advanced concepts like **schema validation**, **custom matchers**, and **dynamic content validation**.

### 1. **Schema Validation (JSON)**

As your responses get more complex, **JSON Schema** validation becomes essential to verify the structure of the response. This helps ensure that responses follow the correct format.

#### Steps:

* Create a JSON schema file (`user-schema.json`) to define the expected structure.
* Validate the response against the schema.

**Example:**

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "data": {
      "type": "object",
      "properties": {
        "id": { "type": "integer" },
        "first_name": { "type": "string" },
        "last_name": { "type": "string" },
        "email": { "type": "string" },
        "address": {
          "type": "object",
          "properties": {
            "street": { "type": "string" },
            "city": { "type": "string" }
          },
          "required": ["street", "city"]
        }
      },
      "required": ["id", "first_name", "last_name", "email", "address"]
    }
  },
  "required": ["data"]
}
```

#### Code to Validate Against Schema:

```java
import static io.restassured.RestAssured.*;

public class SchemaValidation {

    public static void main(String[] args) {
        given()
            .when()
            .get("https://reqres.in/api/users/2")
            .then()
            .assertThat()
            .body(matchesJsonSchemaInClasspath("user-schema.json")); // Validate response against schema
    }
}
```

---

### 2. **Dynamic Validation (Using Variables)**

You may need to validate dynamic content (e.g., values that change per request). You can use variables to capture and validate dynamic parts of the response.

**Example:**

```java
import static io.restassured.RestAssured.*;

public class DynamicValidation {

    public static void main(String[] args) {
        String expectedName = "Janet";

        String response = given()
                            .when()
                            .get("https://reqres.in/api/users/2")
                            .then()
                            .extract().path("data.first_name");

        // Validate dynamic content against expected name
        assert response.equals(expectedName);
    }
}
```

---

## 🔥 Wrapping Up:

* **JSON Validation**: Use path expressions like `data.first_name` to validate JSON responses.
* **XML Validation**: Use XPath to validate XML responses.
* **Schema Validation**: Ensure that the response adheres to a predefined schema.
* **Dynamic Content Validation**: Use variables to validate changing content in your API responses.

---




