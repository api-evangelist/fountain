---
name: Collect and approve applicant documents
description: Upload a secure document for an applicant, link it, approve it, and trigger a recollection in Fountain.
api: llms/fountain-llms.txt
operations:
- POST /v2/applicants/{id}/secure_documents/upload_and_link (Upload File and Link to Applicant)
- GET /v2/applicants/{id}/secure_documents (Get Applicant Files)
- POST /v2/applicants/{id}/secure_documents/approve (Approve Applicant Documents)
- POST /api/v2/applicants/{applicant_id}/file_upload_requests (Trigger file recollection)
---

# Collect and approve applicant documents

Use this flow to manage secure onboarding documents for a Fountain applicant.

## Auth
- OAuth2 client-credentials bearer token on `services.fountain.com` (or legacy `X-ACCESS-TOKEN`).

## Steps
1. **Upload + link** — `POST /v2/applicants/{id}/secure_documents/upload_and_link` to push a file to S3 and attach it to the applicant in one call. Allowed types: PDF, JPG, JPEG, PNG, GIF, HEIC, HEIF, WEBP, AVIF, DOC, DOCX, TXT; max 15 MB. (The two-step `/upload` then `/link_upload` path is also available.)
2. **List** — `GET /v2/applicants/{id}/secure_documents` to read the applicant's files.
3. **Approve** — `POST /v2/applicants/{id}/secure_documents/approve` with the associated data-field keys to approve documents.
4. **Recollect** — `POST /api/v2/applicants/{applicant_id}/file_upload_requests` to notify the applicant to resubmit a file.

## Rules
- Reject files above 15 MB or of an unsupported type before calling the API.
- A webhook (`Posthire — Document Uploaded` / `Posthire — Approve Documents`) fires on these events; verify the `X-OBIQ-SIGNATURE-V2` HMAC-SHA-256 signature. (see `asyncapi/fountain-webhooks.yml`)
