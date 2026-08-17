## 1. Introduction

APIs are frequently called programmatically at high volume by design, which makes missing throttling controls a distinct and common category of risk — covering everything from credential brute-forcing to straightforward denial of service through resource exhaustion.

## 2. What "Unrestricted Resource Consumption" Covers

| Resource | Attack Shape |
|---|---|
| Login/token endpoints | Unlimited authentication attempts — direct brute-force/credential-stuffing enabler, see [[Broken_Authentication_in_APIs]] |
| Expensive query endpoints | Repeated calls to a computation-heavy endpoint (large exports, complex search/filter) exhausting server resources |
| File upload endpoints | Uploading extremely large files, or many files rapidly, exhausting storage/bandwidth |
| Pagination-less list endpoints | An endpoint that returns an entire dataset in one response rather than paginating, enabling both DoS and large-scale data scraping |

## 3. Testing for Missing Rate Limiting

```
1. Send the same request 50-100 times in rapid succession
2. Check response headers for rate-limit signaling (X-RateLimit-Limit, X-RateLimit-Remaining, Retry-After)
3. Confirm whether request N+1 actually gets blocked/throttled, or just keeps succeeding
4. Repeat specifically against authentication endpoints — this is the highest-impact case to confirm
```

Burp Intruder (see [[Burp_Suite_Essentials]]) is the standard tool for firing this volume of requests in a controlled, measurable way during testing.

## 4. Business Logic Resource Abuse

Beyond raw request-count throttling, resource consumption issues also show up in request *shape* — a single, technically-valid request can be crafted to be disproportionately expensive:

```json
// A single GraphQL query requesting deeply nested data can be
// exponentially more expensive to resolve than its size suggests —
// see GraphQL_Security_Testing for the query-complexity-specific version of this issue
{"query": "{ user { friends { friends { friends { friends { name } } } } } }"}
```

```
GET /api/v1/export?format=csv&range=all   ← a single request that triggers a massive, expensive server-side job
```

## 5. What Good Rate Limiting Looks Like (for context)

| Control | Purpose |
|---|---|
| Per-IP and per-account request limits | Caps abuse from a single source regardless of how many accounts are used |
| Explicit pagination with a max page size | Prevents any single request from returning/processing unbounded data |
| Response headers signaling limits | Lets legitimate clients self-throttle, and confirms to a tester that a control exists at all |
| Timeouts/complexity limits on expensive queries | Bounds worst-case cost of a single request regardless of its shape |

## 6. Interview Questions

1. Why are authentication endpoints the highest-priority target when testing for missing rate limiting? → **Unlimited login attempts directly enable brute-force and credential-stuffing attacks against real user accounts — it's the resource-consumption gap with the most severe direct consequence**
2. What's the difference between rate-limiting an endpoint by request count versus by request "shape" or complexity? → **Request-count limiting caps how many times an endpoint is called; complexity-based limiting addresses a single, technically-valid request that's disproportionately expensive to process, such as a deeply nested GraphQL query or an unbounded data export**
3. What response headers indicate an API has rate limiting in place, and how are they useful during testing? → **Headers like X-RateLimit-Limit, X-RateLimit-Remaining, and Retry-After signal that a control exists and how close a client is to hitting it — their absence, combined with requests continuing to succeed past a high volume, indicates the control is missing**
4. Why is a pagination-less list endpoint a resource consumption risk even without any malicious intent? → **Returning an entire dataset in one response means both the server and client must process potentially unbounded data in a single request, which an attacker can exploit deliberately for DoS or bulk scraping**

## 7. Key Points

- Missing rate limiting on **authentication endpoints** is the highest-priority case — it directly enables brute-force and credential stuffing.
- Test by firing a high volume of identical requests and confirming whether later requests are actually throttled, not just checking for the presence of rate-limit headers.
- Resource consumption risk also comes from **request shape/complexity** (nested GraphQL queries, unbounded exports), not just raw request count.
- **Pagination limits, per-account/IP throttling, and query complexity limits** are the standard controls to check for.

---
*Related: [[Broken_Authentication_in_APIs]], [[GraphQL_Security_Testing]], [[API_Security_Assessment_Workflow]]*
