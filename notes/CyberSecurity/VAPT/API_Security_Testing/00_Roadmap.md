## How to Use This Folder

This roadmap gives the recommended reading order for API_Security_Testing — starting with API-specific recon and tooling, moving through the OWASP API Security Top 10 vulnerability classes, into the two modern API technologies (JWTs and GraphQL) that deserve their own deep dive, and finishing with a capstone that ties it all into one assessment workflow. This folder is a direct continuation of Folder 4 — most modern applications are API-driven behind their UI, and several vulnerability classes from Web_Application_Pentesting (broken auth, broken access control) reappear here in an API-specific form.

**Start here:** [[API_Recon_and_Fingerprinting]]
**Finish here:** [[API_Security_Assessment_Workflow]]

## Stage 1 — Recon and Tooling

1. [[API_Recon_and_Fingerprinting]] — discovering endpoints, REST vs GraphQL vs SOAP, Swagger/OpenAPI docs, versioning
2. [[Postman_and_API_Testing_Tooling]] — the standard API-specific client, used alongside Burp Suite from Folder 4

## Stage 2 — OWASP API Security Top 10 Core

3. [[Broken_Object_Level_Authorization_BOLA]] — the API-specific, and most common, form of IDOR
4. [[Broken_Authentication_in_APIs]] — token issuance, validation, and lifecycle weaknesses
5. [[Excessive_Data_Exposure_and_Mass_Assignment]] — APIs returning or accepting more than the client should see or set
6. [[Broken_Function_Level_Authorization]] — reaching admin/privileged endpoints an API client shouldn't be able to call
7. [[Unrestricted_Resource_Consumption_and_Rate_Limiting]] — missing throttling leading to brute-force and denial-of-service exposure

## Stage 3 — Modern API Technologies

8. [[JWT_Attacks_and_API_Token_Security]] — forging, tampering, and abusing JSON Web Tokens
9. [[GraphQL_Security_Testing]] — introspection, nested-query DoS, and authorization gaps specific to GraphQL

## Stage 4 — Synthesis (Capstone)

10. [[API_Security_Assessment_Workflow]] — combining every note above into a realistic assessment, mapped to the OWASP API Security Top 10, and the handoff into Folder 6

## Quick Reference — Full Order

API_Recon_and_Fingerprinting → Postman_and_API_Testing_Tooling → Broken_Object_Level_Authorization_BOLA → Broken_Authentication_in_APIs → Excessive_Data_Exposure_and_Mass_Assignment → Broken_Function_Level_Authorization → Unrestricted_Resource_Consumption_and_Rate_Limiting → JWT_Attacks_and_API_Token_Security → GraphQL_Security_Testing → API_Security_Assessment_Workflow

---
*Related folder: Folder 4, Web_Application_Pentesting — [[Broken_Access_Control_and_IDOR]] and [[Broken_Authentication_and_Session_Management]] are the direct web-app equivalents of several notes here. Folder 6, Active_Directory_and_Internal_Pentesting, picks up once internal access is the focus instead of external APIs.*
