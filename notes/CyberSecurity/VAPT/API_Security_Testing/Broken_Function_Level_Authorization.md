## 1. Introduction

Where [[Broken_Object_Level_Authorization_BOLA]] is about *which object* a caller can reach, **Broken Function Level Authorization (BFLA)** is about *which action/endpoint* a caller can reach at all — the API equivalent of vertical access control from [[Broken_Access_Control_and_IDOR]], applied to entire functions rather than data records.

## 2. The Core Pattern

An API often exposes admin-only functionality on the same base path or infrastructure as regular user functionality, distinguished only by an intended-but-unenforced role check.

```
GET  /api/v1/users/profile          → available to any authenticated user
DELETE /api/v1/users/{id}            → SHOULD require admin role — does it actually check?
POST /api/v1/admin/broadcast-message → SHOULD require admin role — reachable by a regular user token?
```

## 3. Why This Slips Through in Practice

| Cause | Detail |
|---|---|
| Authentication confused with authorization | The endpoint checks "is this caller logged in at all," not "is this caller allowed to call THIS function" |
| Hidden, not disabled | The admin endpoint isn't shown in a regular user's UI/menu, but the backend route itself remains fully reachable — "security through obscurity" |
| Inconsistent enforcement | Role checks applied at the UI/frontend routing layer, but not duplicated at the API layer that actually performs the action |
| HTTP method confusion | `GET /users/{id}` properly restricted, but `DELETE /users/{id}` on the same path forgotten |

## 4. Testing Methodology

```
1. Enumerate every endpoint the API exposes (from documentation, recon, or observed traffic)
2. Identify which ones are intended to be privileged (admin panels, user management, financial actions)
3. Using a LOW-privileged, authenticated token, attempt to call each privileged endpoint directly
4. Test every HTTP verb on a given path independently — GET might be protected while DELETE isn't
5. Also test with NO token and a token for a different, unrelated role, for the same endpoints
```

## 5. Real-World Impact

BFLA findings tend to be high-severity because the "function" being reached often has direct, irreversible impact — deleting other users' accounts, modifying pricing, issuing refunds, or broadcasting messages — rather than just reading data, which makes it one of the more business-critical categories in the OWASP API Security Top 10.

## 6. Interview Questions

1. What's the difference between BOLA and BFLA? → **BOLA concerns whether a caller can access a specific object (data-level); BFLA concerns whether a caller can invoke a specific function or endpoint at all (function/action-level), regardless of which object it targets**
2. Why does hiding an admin endpoint from a regular user's UI not count as securing it? → **The backend route itself remains fully reachable by anyone who knows or discovers the URL — hiding it from the menu is obscurity, not an actual authorization check**
3. Why should every HTTP verb on a given endpoint path be tested independently for BFLA? → **A path can have inconsistent authorization per verb — for example, GET might correctly require admin privileges while DELETE on that same path was overlooked**
4. Why are BFLA findings often rated more severely than a typical data-exposure bug? → **The reachable functions often perform direct, sometimes irreversible actions — deleting accounts, issuing refunds, changing prices — rather than just exposing data, giving them a much higher potential business impact**

## 7. Key Points

- **BFLA** is about unauthorized access to entire functions/endpoints, not specific data objects — the API-layer equivalent of vertical access control.
- Commonly caused by confusing **authentication** ("is this user logged in") with **authorization** ("is this user allowed to do THIS").
- Hidden UI elements are not a security control — the underlying route must be tested directly with a low-privileged token.
- Test **every verb independently** on every endpoint path — protection is often inconsistent across GET/POST/PUT/DELETE on the same path.

---
*Related: [[Broken_Object_Level_Authorization_BOLA]], [[Broken_Access_Control_and_IDOR]], [[API_Security_Assessment_Workflow]]*
