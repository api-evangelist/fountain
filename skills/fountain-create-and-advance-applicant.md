---
name: Create and advance an applicant
description: Create an applicant in a Fountain opening and move them through the hiring funnel, reading transition history along the way.
api: llms/fountain-llms.txt
operations:
- POST /v2/applicants (Create an Applicant)
- GET /v2/applicants/{id} (Get Applicant Info)
- PUT /v2/applicants/{id}/advance (Advance an Applicant)
- GET /v2/applicants/{id}/transitions (Get Transition History)
- POST /v2/applicants/notify (Notify applicant)
---

# Create and advance an applicant

Use this flow to add a candidate to a Fountain opening and progress them through stages.

## Auth
- Get a bearer token from `POST https://services.fountain.com/api/servicesecurity/processes/apikey/oauth/token` (OAuth2 client-credentials); it is valid 60 minutes. Send it as an `Authorization` bearer header. (Legacy tenants instead send `X-ACCESS-TOKEN` against `api.fountain.com/v2`.)

## Steps
1. **Create the applicant** — `POST /v2/applicants` with the applicant's data and the target `opening_id`. Capture the returned applicant `id`.
2. **(Optional) Notify** — `POST /v2/applicants/notify` to SMS/email the applicant a portal link to continue their application.
3. **Read current state** — `GET /v2/applicants/{id}` to confirm the applicant's current stage.
4. **Advance** — `PUT /v2/applicants/{id}/advance` to move the applicant to the next stage in the opening's workflow. For bulk moves use `POST /v2/applicants/advance`.
5. **Audit** — `GET /v2/applicants/{id}/transitions` to read the transition history and confirm the move landed.

## Rules
- Honor the 120 requests/minute limit; back off on `429` using `X-Api-Ratelimit-Reset`. (see `rate-limits/fountain-rate-limits.yml`)
- Errors are JSON with standard HTTP status codes (`401/403/404/422`). (see `errors/fountain-problem-types.yml`)
- To erase a candidate under GDPR, call `POST /v2/applicants/{id}/gdpr_anonymize` — it is idempotent (a no-op if already anonymized).
