## 1. Introduction

A **proxy server** is an intermediary that sits between a client and a destination server, forwarding requests on behalf of one side. Proxies operate primarily at the **Application layer (Layer 7)**, giving them visibility into and control over the actual content of requests — not just IP/port like a basic firewall or router. The two fundamental categories, **forward** and **reverse** proxies, serve very different purposes despite the shared underlying concept.

## 2. Forward Proxy

Sits **in front of clients**, forwarding their requests to external servers on their behalf — the destination server sees the proxy's IP, not the client's.

```
Client → Forward Proxy → Internet/Destination Server
```

**Use cases:**
- **Content filtering** — organizations block access to certain websites/categories.
- **Anonymity/Privacy** — hides the client's real IP from the destination.
- **Caching** — stores frequently requested content locally to reduce bandwidth and improve speed.
- **Bypassing geo-restrictions** — routing requests through a proxy in a different region.
- **Monitoring/Logging** — organizations log employee internet usage for compliance/security.

## 3. Reverse Proxy

Sits **in front of servers**, receiving client requests and forwarding them to one or more backend servers — the client only ever sees the reverse proxy's identity, never the actual backend servers directly.

```
Client → Internet → Reverse Proxy → Backend Server(s)
```

**Use cases:**
- **Load balancing** — distributing requests across multiple backend servers.
- **SSL/TLS termination** — handling encryption/decryption centrally so backend servers don't each need to manage certificates (see [[TLS_SSL]] in the Cryptography folder).
- **Caching** — serving cached static content without hitting the backend.
- **Security/obfuscation** — hides backend server topology/IPs from direct exposure, can filter malicious requests before they reach the application (often combined with WAF functionality — see [[Firewalls]]).
- **Compression** — reduces response size before sending to the client.

**Common tools:** Nginx, HAProxy, Envoy, Cloudflare, AWS Application Load Balancer.

## 4. Forward vs Reverse Proxy — Quick Comparison

| Aspect | Forward Proxy | Reverse Proxy |
|---|---|---|
| Represents | The client | The server |
| Client awareness | Client knows it's using a proxy (usually configured explicitly) | Client typically unaware — it just thinks it's talking to the real server |
| Hides | Client's identity from the server | Server's identity/topology from the client |
| Typical deployment | Corporate network edge, VPN-adjacent services | In front of web application servers/data centers |

## 5. Proxy vs VPN — Common Confusion

| Aspect | Proxy | VPN |
|---|---|---|
| Scope | Typically application-specific (e.g., just browser traffic, or a specific protocol) | Encrypts and tunnels ALL device traffic system-wide |
| Encryption | Not necessarily encrypted (many proxies just relay traffic in the clear) | Always encrypted (see [[VPN]]) |
| Layer | Application layer (L7) | Network layer (L3), tunnels underlying traffic |

A proxy changes *where your traffic appears to come from* for supported applications; a VPN encrypts and reroutes *all* traffic at the OS/network level.

## 6. Transparent vs Non-Transparent Proxies

| Type | Description |
|---|---|
| Transparent Proxy | Intercepts traffic without requiring client configuration — the client is unaware a proxy is involved (common in ISP-level or corporate network filtering) |
| Non-Transparent (Explicit) Proxy | Client is explicitly configured to send traffic through the proxy (e.g., manually configuring proxy settings in a browser) |

## 7. Load Balancing (Closely Related to Reverse Proxies)

A **load balancer** distributes incoming traffic across multiple backend servers, often implemented as a specialized reverse proxy function.

**Common algorithms:**

| Algorithm | Description |
|---|---|
| Round Robin | Requests distributed sequentially across servers in rotation |
| Least Connections | Sends new requests to the server with the fewest active connections |
| IP Hash | Routes based on a hash of the client's IP, ensuring the same client consistently reaches the same backend (useful for session persistence) |
| Weighted Round Robin | Like round robin, but servers with more capacity receive proportionally more requests |

**Layer 4 vs Layer 7 load balancing:** L4 balances based on IP/port (faster, protocol-agnostic); L7 balances based on actual request content like URL path or headers (smarter routing, e.g., sending `/api/*` to one server pool and `/images/*` to another).

## 8. API Gateway (Specialized Reverse Proxy)

A modern extension of the reverse proxy concept specifically for API traffic — handles routing to multiple backend microservices, authentication/authorization, rate limiting, request/response transformation, and centralized logging for an API ecosystem. Conceptually a reverse proxy with API-specific intelligence layered on top.

## 9. Security Considerations

| Concern | Description |
|---|---|
| Open Proxies | Misconfigured or intentionally public proxies can be abused by attackers to anonymize malicious traffic, evading IP-based blocking |
| Proxy as MITM | A proxy inherently sees (and can modify) all traffic passing through it — trusting a malicious or compromised proxy is equivalent to a built-in MITM position |
| Reverse Proxy Misconfig | Improperly configured reverse proxies can leak internal backend information (headers, error messages) or fail to properly validate/sanitize forwarded requests |
| SSRF (Server-Side Request Forgery) | Attackers can sometimes abuse a server's own proxy-like request-forwarding behavior to reach internal-only resources it shouldn't be able to access |

## 10. Interview Questions

1. What's the fundamental difference between a forward and reverse proxy? → **Forward proxy represents/hides the client; reverse proxy represents/hides the server**
2. Why would an organization deploy a reverse proxy in front of its web servers? → **Load balancing, SSL termination, caching, and hiding backend server topology**
3. Is a proxy the same as a VPN? → **No — a proxy is typically application-specific and not always encrypted; a VPN encrypts and tunnels all system traffic**
4. What's the difference between a transparent and explicit proxy? → **Transparent intercepts traffic without client configuration; explicit requires the client to be configured to use it**
5. What's the difference between Layer 4 and Layer 7 load balancing? → **L4 balances based on IP/port only; L7 balances based on actual application content like URL path or headers**
6. What security risk involves a server being tricked into making requests to internal-only resources? → **SSRF (Server-Side Request Forgery)**

## 11. Key Points

- **Forward proxies** represent clients (hide client identity, filter/cache outbound requests); **reverse proxies** represent servers (load balance, terminate SSL, hide backend topology).
- Proxies operate at **Layer 7**, giving them content-level visibility that basic firewalls/routers lack.
- A **proxy is not the same as a VPN** — proxies are typically narrower in scope and not always encrypted.
- **Load balancers** are a specialized reverse proxy function, operating at Layer 4 (fast, IP/port-based) or Layer 7 (smart, content-aware).
- Security risks include **open proxy abuse**, **MITM exposure**, and **SSRF** against misconfigured reverse proxies.

---
*Related: [[Network_Devices]], [[Firewalls]], [[VPN]], [[TLS_SSL]]*
