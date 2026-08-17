## 1. Introduction

APIs have their own recon layer distinct from the page-crawling approach in [[Web_App_Recon_and_Fingerprinting]] — there's no UI to click through, so mapping an API means finding its documentation, its endpoints, and its data shapes directly.

## 2. API Architectural Styles

| Style | Characteristics | Recon Implication |
|---|---|---|
| **REST** | Resource-based URLs, HTTP verbs carry meaning (GET/POST/PUT/DELETE) | Endpoint structure is often predictable/enumerable (`/users/{id}`, `/orders/{id}`) |
| **GraphQL** | Single endpoint, client specifies exactly what data it wants in the query | Recon centers on the schema itself — see [[GraphQL_Security_Testing]] |
| **SOAP** | XML-based, strict WSDL-defined contracts | Recon centers on locating and parsing the WSDL file |

## 3. Finding API Documentation

```
/swagger.json
/swagger-ui.html
/openapi.json
/api-docs
/graphql            (often accepts introspection queries directly)
```

| Source | Value |
|---|---|
| **Swagger / OpenAPI spec** | Often documents every endpoint, parameter, and expected data type — the single highest-value recon find |
| **Postman public collections** | Developers sometimes publish/share collections that leak internal endpoint structure |
| **JavaScript source / mobile app decompilation** | Frontend code frequently hardcodes API endpoints, sometimes even API keys |
| **GitHub / GitLab search** | Leaked API keys, internal API documentation, or Postman collections committed by mistake |

## 4. Endpoint Discovery Without Documentation

```
ffuf -u https://api.target.com/FUZZ -w api_wordlist.txt
```

When no documentation is exposed, endpoint discovery falls back to brute-forcing common resource/action names, and to observing patterns: if `/users/1042` exists, `/users/1041` and `/users/1043` almost certainly do too — directly setting up the [[Broken_Object_Level_Authorization_BOLA]] testing in Stage 2.

## 5. API Versioning

```
/api/v1/users
/api/v2/users
Header: Accept: application/vnd.company.v1+json
```

Older API versions are frequently left running, undocumented, and unpatched, alongside the current one — always check whether an older version accepts the same requests with weaker validation or authorization than the current version.

## 6. Interview Questions

1. Why is finding a Swagger/OpenAPI spec considered the single highest-value API recon find? → **It typically documents every endpoint, parameter, and expected data type directly, eliminating most of the guesswork that brute-force discovery would otherwise require**
2. How does predictable REST URL structure aid an attacker beyond simple endpoint discovery? → **Once one resource ID is confirmed (e.g. `/users/1042`), it strongly implies neighboring IDs exist too, directly setting up authorization testing like BOLA against those neighboring resources**
3. Why should a tester specifically check for older API versions still running in parallel with the current one? → **Older versions are often left undocumented and unpatched, sometimes lacking validation or authorization checks that were added to the current version — the same functionality with weaker security**
4. Name one non-obvious source where API endpoint structure or credentials can leak. → **Any reasonable example: frontend JavaScript source/mobile app decompilation, publicly shared Postman collections, or committed source code on GitHub/GitLab**

## 7. Key Points

- API recon centers on finding **documentation** (Swagger/OpenAPI, WSDL) rather than crawling pages — it's the highest-leverage find available.
- **REST** endpoints are often predictable and enumerable; **GraphQL** recon instead centers on the schema (see [[GraphQL_Security_Testing]]).
- Frontend source code, leaked Postman collections, and code repositories are common indirect sources of endpoint and credential leakage.
- Always check for **older, parallel API versions** — they're a common source of weaker, forgotten validation and authorization logic.

---
*Related: [[Web_App_Recon_and_Fingerprinting]], [[Postman_and_API_Testing_Tooling]], [[Broken_Object_Level_Authorization_BOLA]]*
