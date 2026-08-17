## 1. Introduction

While Burp Suite ([[Burp_Suite_Essentials]]) remains the core interception proxy for API testing too, **Postman** is the standard purpose-built client for constructing, organizing, and iterating on API requests — most API pentests use both together: Postman to build and organize well-formed requests, Burp to intercept, modify, and fuzz them.

## 2. Core Postman Concepts

| Concept | Purpose |
|---|---|
| **Collection** | An organized set of saved requests for a given API — often importable directly from a target's published Swagger/OpenAPI spec |
| **Environment** | A set of variables (base URL, auth tokens, IDs) swappable between dev/staging/prod without editing every request |
| **Pre-request script** | JavaScript run before a request fires — commonly used to generate or refresh auth tokens automatically |
| **Tests / assertions** | Scripted checks run against the response — useful for confirming whether an authorization bypass attempt actually succeeded |

## 3. Importing an API Definition

```
File → Import → paste Swagger/OpenAPI URL or upload the JSON/YAML
```

Importing a discovered API spec (see [[API_Recon_and_Fingerprinting]]) instantly builds a full collection of every documented endpoint with the expected parameters pre-filled — the fastest way to go from "found the docs" to "testing every endpoint."

## 4. Routing Postman Through Burp

```
Postman Settings → Proxy → set proxy server to 127.0.0.1:8080 (Burp's listening port)
```

Chaining Postman through Burp's proxy combines the strengths of both: Postman's clean request organization and variable management, with Burp's Repeater/Intruder for manual tampering and Site map for building a persistent record of every request tested.

## 5. A Typical API Testing Workflow

```
1. Import the API spec (or manually build a collection from recon findings)
2. Set up an Environment with a valid auth token for a low-privileged test account
3. Route traffic through Burp's proxy
4. Systematically work through each endpoint:
   - Swap the token for a different user's token (authentication/BOLA testing)
   - Remove the token entirely (authentication enforcement testing)
   - Modify object IDs in the URL/body (BOLA testing)
   - Add unexpected fields to the request body (mass assignment testing)
5. Send interesting requests to Burp Repeater for focused manual iteration
```

## 6. Interview Questions

1. Why do API pentests commonly use Postman and Burp Suite together rather than just one? → **Postman is better for cleanly building, organizing, and managing collections of well-formed API requests (with variables and environments); Burp is better for intercepting, tampering with, and fuzzing those requests at the raw HTTP level**
2. What's the fastest way to build a complete test collection once an API's documentation has been found? → **Importing the discovered Swagger/OpenAPI spec directly into Postman, which auto-generates a full collection of every documented endpoint with expected parameters pre-filled**
3. What's a practical use of Postman's pre-request scripts during authenticated API testing? → **Automatically generating or refreshing an auth token before each request fires, so a testing session doesn't break every time a token expires**
4. What does routing Postman's traffic through Burp's proxy enable that Postman alone doesn't? → **It lets every Postman-issued request be intercepted, modified on the fly, sent to Repeater/Intruder for manual or automated tampering, and logged in Burp's persistent Site map**

## 7. Key Points

- **Postman** is the standard tool for building and organizing well-formed API requests, especially from an imported Swagger/OpenAPI spec.
- **Environments** and **variables** let the same collection be reused across auth tokens, users, and target environments without manual edits.
- Routing Postman through **Burp's proxy** combines clean request management with Burp's tampering and fuzzing capabilities.
- A systematic workflow — swap tokens, drop tokens, alter IDs, add extra fields — covers the core OWASP API Top 10 classes covered next in this folder.

---
*Related: [[API_Recon_and_Fingerprinting]], [[Burp_Suite_Essentials]], [[Broken_Object_Level_Authorization_BOLA]]*
