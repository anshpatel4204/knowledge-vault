## 1. Introduction

**BOLA (Broken Object Level Authorization)** sits at the top of the OWASP API Security Top 10, and is functionally the API-specific form of [[Broken_Access_Control_and_IDOR]] — an endpoint accepts an object identifier and returns or modifies that object without verifying the authenticated caller actually owns or is authorized to access it.

## 2. Why BOLA Is Especially Common in APIs

| Reason | Detail |
|---|---|
| Object IDs are structural | REST APIs are built around resource identifiers by design (`/orders/{id}`), unlike a web UI where the ID might be hidden behind session state |
| Authorization logic is easy to skip | Many frameworks auto-generate CRUD endpoints from data models, and it's easy to add authentication without adding a matching per-object ownership check |
| No visual cue | A web page might render an obvious "access denied" UI; an API just returns JSON, so subtle authorization gaps are less likely to be noticed during development |

## 3. Testing Methodology

```
1. Authenticate as User A, capture a request for A's own resource
   GET /api/v1/orders/1042   (returns User A's order, 200 OK)
2. Using the SAME token (still User A), request User B's known resource ID
   GET /api/v1/orders/1043   (should be 403/404 — is it?)
3. Repeat across every endpoint that accepts an object ID: GET, PUT, DELETE, and nested resources
   GET /api/v1/users/5/invoices/9001
4. Test with no token, and with a token from a different privilege level, for the same effect
```

## 4. Beyond Numeric IDs

| ID Type | Testing Note |
|---|---|
| Sequential integers | Trivially enumerable — the easiest case to test and the easiest for an attacker to exploit at scale |
| UUIDs | Not guessable, but still vulnerable if leaked elsewhere (another response, an error message, a different user's own data referencing them) |
| Nested object references | `/api/v1/companies/{companyId}/invoices/{invoiceId}` — every ID in the path needs its own ownership check, not just the final one |

## 5. Why This Is Rated So Severely

Unlike a web-page IDOR that might expose one record type, an API's BOLA often applies uniformly across dozens of endpoints generated from the same underlying data model — a single missing ownership check in a shared authorization middleware can expose the *entire* dataset behind that endpoint pattern, not just one record.

## 6. Interview Questions

1. What's the core difference between testing BOLA and testing an IDOR on a traditional web page? → **The technique is identical (swap an object identifier while authenticated as a different user) — the difference is that API endpoints are structurally built around explicit resource identifiers, making BOLA both easier to find systematically and often present across many endpoints generated from the same data model**
2. Why don't UUIDs fully protect against BOLA the way they protect against simple enumeration? → **A UUID isn't guessable, but it's still exploitable if the authorization check is missing — an attacker who obtains a valid UUID for another user's object (via leakage elsewhere) can still access it without an ownership check in place**
3. Why do nested resource paths require testing every ID in the path, not just the final one? → **Each identifier in a nested path (e.g. companyId and invoiceId) represents a potential authorization boundary — checking only the final ID can miss a case where the outer object reference itself isn't validated against the caller**
4. Why is a single missing ownership check in shared middleware considered especially severe for an API? → **Because many API endpoints are often auto-generated from the same underlying data model and share the same authorization logic, one missing check can expose the entire dataset across many endpoints rather than a single isolated page**

## 7. Key Points

- **BOLA** is the API-specific form of IDOR, and the #1-ranked OWASP API Security risk — swap an object ID while authenticated as another user to test it.
- Test **every** verb (GET/PUT/DELETE) and **every** ID in **nested** resource paths, not just the outermost one.
- **UUIDs mitigate guessing, not the missing authorization check itself** — a leaked or observed UUID is still exploitable.
- BOLA findings often apply across many endpoints sharing the same data model, making a single missing check a large-scale exposure.

---
*Related: [[Broken_Access_Control_and_IDOR]], [[API_Recon_and_Fingerprinting]], [[Broken_Function_Level_Authorization]]*
