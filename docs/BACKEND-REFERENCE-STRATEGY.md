# WhatsApp Backend Reference Strategy

**Repository:** `gantulu/whatsapp-v5`  
**Document status:** Baseline V1  
**Scope:** Technical references for implementing a Supabase backend integrated with the Meta WhatsApp Cloud API.

## 1. Purpose

This document establishes the reference hierarchy used to design, implement, test, and maintain the WhatsApp backend. It separates Meta's product/API contract from Postman's executable examples and Supabase's backend implementation guidance.

## 2. Reference hierarchy

| Priority | Source | Role |
|---|---|---|
| Primary — Meta | [Meta WhatsApp Business Platform Documentation](https://developers.facebook.com/documentation/business-messaging/whatsapp/) | Source of truth for WhatsApp product behavior, Cloud API capabilities, endpoint semantics, permissions, authentication requirements, webhooks, messaging rules, limits, and platform policies. |
| Secondary — Meta Postman | [Meta Official WhatsApp Business Platform workspace](https://www.postman.com/meta/whatsapp-business-platform/overview) · [WhatsApp Cloud API collection](https://www.postman.com/meta/whatsapp-business-platform/collection/wlk6lh4/whatsapp-cloud-api) | Practical API reference for request construction, parameters, headers, example payloads, response inspection, and endpoint testing. |
| Primary for Supabase implementation | [Supabase Documentation](https://supabase.com/docs) | Source of truth for Supabase-specific architecture and behavior: Edge Functions, PostgreSQL, Database Webhooks, Row Level Security (RLS), database privileges, Storage, secrets, and deployment/runtime configuration. |

**Important distinction:** Meta documentation governs the WhatsApp API contract. Supabase documentation governs how Supabase services work. Postman examples help exercise the API; they do not override either provider's normative documentation.

## 3. Rules for resolving discrepancies

1. For WhatsApp API behavior, permissions, supported fields, webhook events, limits, and policy requirements, follow the latest applicable Meta documentation.
2. For Supabase behavior, SQL, RLS, grants, Edge Functions, Storage, secrets, and runtime constraints, follow the latest applicable Supabase documentation.
3. Treat Postman request examples as implementation/test examples. If an example conflicts with current Meta documentation, verify against Meta documentation and a controlled API test before use.
4. Do not assume that a successful Postman request proves the complete backend flow is secure, idempotent, or production-ready.
5. Record material discrepancies, the affected API/version, verification evidence, and the resulting implementation decision in the relevant technical document.

## 4. Implementation workflow

1. **Specify:** Identify the required WhatsApp use case and locate the relevant Meta documentation, including the applicable Graph API version.
2. **Map:** Identify the corresponding Postman request and map its method, URL, path parameters, query parameters, headers, body, and expected response.
3. **Design:** Map the operation to Supabase components. Keep privileged provider credentials and server-side operations out of the frontend.
4. **Secure:** Define authorization, least-privilege database grants, RLS policies, input validation, webhook verification, replay/deduplication handling, and secret handling as applicable.
5. **Implement:** Build the Supabase database and Edge Function changes only after the relevant contracts and design decisions are established.
6. **Verify:** Test success, validation failures, provider errors, authorization failures, duplicate delivery/retry behavior, and relevant webhook lifecycle cases.
7. **Maintain:** Re-check the applicable Meta Graph API version and provider documentation when changing or extending an integration.

## 5. Backend design guardrails

- The frontend must never contain the Meta access token, Supabase service-role key, or other privileged secrets.
- Store provider credentials using Supabase-supported server-side secret management; do not commit credentials or real tokens to the repository.
- Treat inbound webhooks as untrusted input. Verify authenticity according to Meta's current webhook requirements before processing events.
- Design webhook processing to tolerate retries and duplicate deliveries; use stable provider event/message identifiers where applicable.
- Apply least privilege to database roles, grants, RLS policies, and Storage access. Do not rely on frontend checks as backend authorization.
- Validate all provider responses and persist enough operational state to support diagnosis and reconciliation.
- Pin and document the Graph API version used by the integration; do not silently assume that examples or unversioned references represent the deployed version.

These are engineering guardrails, not a finalized schema or implementation specification. Exact mechanisms must be verified against the current provider documentation and the approved project requirements.

## 6. Change control

When a source URL, API version, endpoint, permission, payload, or Supabase behavior changes:

- identify the affected backend component and documentation;
- verify the change against the relevant primary source;
- update request examples/tests where needed;
- document compatibility and migration impact before implementation.

## 7. Official reference links

- Meta WhatsApp Business Platform: https://developers.facebook.com/documentation/business-messaging/whatsapp/
- Meta Cloud API overview: https://developers.facebook.com/docs/whatsapp/cloud-api/overview
- Meta Official Postman workspace: https://www.postman.com/meta/whatsapp-business-platform/overview
- Meta WhatsApp Cloud API Postman collection: https://www.postman.com/meta/whatsapp-business-platform/collection/wlk6lh4/whatsapp-cloud-api
- Supabase documentation: https://supabase.com/docs
- Supabase Edge Functions: https://supabase.com/docs/guides/functions
- Supabase Database: https://supabase.com/docs/guides/database/overview
- Supabase Row Level Security: https://supabase.com/docs/guides/database/postgres/row-level-security

---

**Baseline decision:** Meta Developer Documentation is the primary source of truth for WhatsApp API behavior; Meta's official Postman collection is the supporting API implementation and testing reference; Supabase official documentation is the source of truth for Supabase backend implementation.
