## 1. Introduction

**GraphQL** inverts the typical REST model — instead of many fixed endpoints, a single endpoint accepts flexible, client-defined queries. That flexibility is powerful for developers and introduces a distinct set of testing considerations not present in REST APIs.

## 2. Introspection

GraphQL APIs can expose a **schema introspection** query that returns the entire API's structure — every type, field, query, and mutation available — effectively self-documenting the whole attack surface if left enabled in production.

```graphql
query {
  __schema {
    types { name fields { name type { name } } }
  }
}
```

Testing this is a single request: send the introspection query to the GraphQL endpoint (commonly `/graphql`) and see if it responds with the full schema. Production APIs *should* disable introspection, but frequently don't.

## 3. Authorization Gaps Per Field

A critical structural difference from REST: authorization in GraphQL needs to be enforced **per field/resolver**, not just per endpoint, because a single query can request many different pieces of data from many different resolvers at once.

```graphql
query {
  user(id: 1042) {
    name
    email
    internalAdminNotes   # is THIS specific field actually authorization-checked?
  }
}
```

A resolver-level authorization gap can leak a sensitive field even if the overall query and top-level object access are properly restricted — directly overlapping with [[Excessive_Data_Exposure_and_Mass_Assignment]] but specific to GraphQL's field-by-field resolution model.

## 4. Nested Query / Resource Exhaustion (Query Complexity DoS)

Because clients define query shape, a maliciously deep or wide query can force the server to perform exponentially more work than the request's size suggests:

```graphql
query {
  user(id: 1) {
    friends { friends { friends { friends { friends { name } } } } }
  }
}
```

Combined with **batching** (sending many queries/mutations in a single request), this becomes a potent, low-effort denial-of-service vector against APIs that don't enforce query depth or complexity limits — see [[Unrestricted_Resource_Consumption_and_Rate_Limiting]].

## 5. Other GraphQL-Specific Considerations

| Issue | Detail |
|---|---|
| Batching attacks | Many login/guess attempts bundled into a single HTTP request, bypassing naive per-request rate limiting |
| Suggestion/error-based schema leakage | Some servers suggest field names in error messages ("did you mean X?") even with introspection disabled, partially rebuilding the schema |
| Mutation-based BOLA/BFLA | The same object- and function-level authorization issues from earlier notes apply to GraphQL mutations, just expressed differently |

## 6. Testing Tools

```
graphql-cocktail / InQL / GraphQL Voyager    # schema visualization from introspection results
clairvoyance                                  # attempts schema reconstruction even with introspection disabled
```

## 7. Interview Questions

1. Why is a GraphQL introspection query considered such a high-value recon find? → **It can return the API's entire schema — every type, field, query, and mutation — in a single request, self-documenting the whole attack surface at once, which is far more efficient than the endpoint-by-endpoint discovery REST requires**
2. Why must authorization in GraphQL be enforced per resolver rather than just per query or endpoint? → **A single GraphQL query can request many different fields from many different underlying resolvers at once, so a missing check on any individual field's resolver can leak sensitive data even when the overall query and top-level object access are otherwise properly restricted**
3. How can a maliciously crafted query cause denial of service in GraphQL specifically? → **Deeply nested or wide queries can force the server to perform exponentially more resolution work than the request's raw size suggests, and this can be compounded by batching many such queries into a single HTTP request**
4. What is a GraphQL batching attack, and why can it bypass naive rate limiting? → **Bundling many separate queries or mutations (e.g. multiple login attempts) into a single HTTP request — rate limiting applied per HTTP request rather than per logical operation inside it fails to catch the multiple attempts hidden within one request**

## 8. Key Points

- **Introspection**, if left enabled in production, hands over the entire API schema in one request — always check first.
- GraphQL authorization must be enforced **per field/resolver**, not just per query — a structural difference from REST that's easy to get wrong.
- **Nested queries and batching** create a distinct, GraphQL-specific denial-of-service surface beyond simple request-count rate limiting.
- Tools like **InQL** and **clairvoyance** help visualize or reconstruct the schema, even when introspection is disabled.

---
*Related: [[API_Recon_and_Fingerprinting]], [[Excessive_Data_Exposure_and_Mass_Assignment]], [[Unrestricted_Resource_Consumption_and_Rate_Limiting]]*
