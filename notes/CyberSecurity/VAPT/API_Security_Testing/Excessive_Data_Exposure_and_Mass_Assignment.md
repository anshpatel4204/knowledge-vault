## 1. Introduction

Two distinct but frequently paired API weaknesses: an API returning more data than the client's UI displays (excessive data exposure), and an API accepting more fields than the client's UI intends to submit (mass assignment). Both stem from the same root pattern — trusting the frontend to filter data, instead of enforcing that filtering server-side.

## 2. Excessive Data Exposure

Many APIs are built to return a full internal object and rely on the frontend to only *display* the relevant fields — but an attacker talking to the API directly (not through the UI) sees everything the response actually contains.

```json
// Frontend only displays "name" and "email", but the raw response includes:
{
  "id": 1042,
  "name": "Jane Doe",
  "email": "jane@company.com",
  "password_hash": "$2b$12$...",
  "internal_notes": "flagged for fraud review",
  "ssn": "xxx-xx-xxxx"
}
```

Testing this is simple: **inspect the full raw API response**, not just what the associated UI renders — Burp's Proxy history or Postman's raw response view is all that's needed.

## 3. Mass Assignment

The mirror-image problem: a backend that automatically binds every field in a request body to an internal object, without an explicit allow-list of which fields the client is actually permitted to set.

```json
// Intended request:
{ "name": "Jane Doe", "email": "jane@company.com" }

// Attacker adds fields never shown in the UI, hoping the backend blindly binds them:
{ "name": "Jane Doe", "email": "jane@company.com", "role": "admin", "isVerified": true, "accountBalance": 999999 }
```

If any of those extra fields are accepted and applied, that's a mass assignment vulnerability — and depending on the field, ranges from account privilege escalation to direct financial/business-logic manipulation.

## 4. Testing Methodology

```
1. Capture a legitimate request/response pair for an object the tester controls
2. For exposure: read the FULL response body — every field, not just what the UI shows
3. For mass assignment: take a legitimate request and add plausible extra fields
   (role, isAdmin, verified, price, discount, ownerId, status — guess based on the data model)
4. Resend, and check whether the extra field was silently accepted and applied
5. Confirm impact by re-fetching the object and checking if the new field value stuck
```

## 5. Why This Pair Is Distinct From BOLA

BOLA is about *whose* object is being accessed; excessive data exposure and mass assignment are about *which fields* of an object (even the caller's own object) should be readable or writable — a user might be fully authorized to view/edit their own profile, but still shouldn't be able to read their own password hash or set their own `role` field to `admin`.

## 6. Interview Questions

1. What's the core difference between excessive data exposure and mass assignment? → **Excessive data exposure is about the API returning more fields than intended in a response; mass assignment is the opposite direction — the API accepting and applying more fields than intended from a request**
2. Why is inspecting the raw API response (rather than the rendered UI) essential for finding excessive data exposure? → **The UI only displays a filtered subset of fields; the raw response often contains the full internal object, including sensitive fields the frontend simply chooses not to render**
3. Describe a basic mass assignment test. → **Take a legitimate request for an object the tester controls, add plausible extra fields not shown in the UI (e.g. `role: admin`), resend it, and check whether the backend silently accepted and applied that field**
4. Why can BOLA testing pass on an endpoint that's still vulnerable to mass assignment? → **BOLA testing confirms whether a user can access someone ELSE's object; mass assignment can be exploited entirely within a user's OWN object by setting fields on it that shouldn't be client-writable at all, like role or verification status**

## 7. Key Points

- **Excessive data exposure**: the API response contains more than the UI shows — always inspect the raw response, not the rendered page.
- **Mass assignment**: the API blindly binds extra request fields to an internal object — test by adding plausible extra fields to a legitimate request.
- Both stem from **trusting the frontend to filter data** instead of enforcing field-level allow-lists server-side.
- Distinct from BOLA — these vulnerabilities can exist entirely within a user's own, fully-authorized object.

---
*Related: [[Broken_Object_Level_Authorization_BOLA]], [[Broken_Function_Level_Authorization]], [[Postman_and_API_Testing_Tooling]]*
