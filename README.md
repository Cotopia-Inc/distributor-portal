Updated README — Cotopia Distributor Presign Client
==================================================

Last updated: October 2025

Overview
--------
This repository contains the Cotopia Distributor Presign Client — a lightweight, browser-based admin/client UI that demonstrates and exercises Cotopia’s presigned upload / presigned GET flow for attachments (images/videos) and a simple posts-and-replies workflow. The client is intended for use by authorized Cotopia Distributors and Recruited Distributors as part of the Cotopia Distributor Program and is provided subject to the Master Distribution / Reseller Licensing and Distributorship Agreement and the applicable Product Agreement (see licensing and distribution section below).

This README covers:
- What the app is and does
- How it relates to the Distributor Program and Product Agreement
- Installation, configuration and deployment steps
- API contract expected on the server side
- Security, operational and compliance guidance for Distributors
- Onboarding checklist for Recruited Distributors
- Testing, troubleshooting and support contacts
- Packaging and redistribution notes for Distributor use

Project contents
----------------
- `index.html` — single-file client (HTML/CSS/JS) UI (the presign client you were given).  
- `README.md` — this document.  
- `LICENSE` — licensing terms (if provided in repo). If not provided, distribute only per Distributor Agreement.  
- `examples/` (optional) — server examples (Node/Express, etc.) if included.  
- `exhibits/` — program legal exhibits (Product Agreement, Exhibit F onboarding form templates) if provided.

What the client does
--------------------
- Allows users to sign up / sign in (Distributor or Admin roles).  
- Stores session JWT in `localStorage` (key: `dist_api_token`) and user metadata under `dist_api_user`.  
- Admins can create posts with HTML content, choose styling (font, color), and attach image/video files.  
- For each attachment the client requests a presigned PUT URL from the server (`POST /api/upload-url`), uploads the file directly to the storage endpoint (S3-compatible) using HTTP `PUT`, and then submits post metadata (including S3 object keys) to the server (`POST /api/posts`).  
- For display and downloads, the client requests presigned GET URLs from the server (`POST /api/download-url`) to generate time-limited, private access links.  
- Distributor-only Included Tech Support model: the client contains UI elements reflecting admin-only flows; actual support obligations are defined in the Product Agreement and Master Agreement.  
- Supports recruiting downstream Distributors (workflow is supported by Product Agreement Exhibit F but onboarding and remittance are operational activities external to this client).

Relationship to the Distributor Program
---------------------------------------
- This client is a distributable asset to be provided to authorized Distributors under the Master Agreement and the applicable Product Agreement (Exhibit A). Redistribution, white-labeling, and rehosting rights depend on the Tier selected in the Product Agreement (Tier 1–4). White-label / OEM use requires explicit written authorization in the Product Agreement.  
- Use of Cotopia Marks, branding and co-branded materials when packaging or redistributing this client is conditioned on timely payment of Admin and Continued License & Tech Support Fees and compliance with Section 13 of the Master Agreement. Distributors must follow Exhibit D (Brand Guidelines) for permitted uses.  
- If Distributor recruits downstream Recruited Distributors, Distributor must follow Exhibit F and remit initial fees ($79) to Company within five (5) business days as specified in the Product Agreement. Distributor may collect and retain the Admin Fee from Recruited Distributors only where authorized.

Quick start (local test deployment)
----------------------------------
Prerequisites:
- Node.js (recommended v16+ for example server)
- A storage service that supports presigned PUT/GET (Amazon S3 or S3-compatible).  
- Server-side API implementing endpoints described below.

Steps:
1. Open `index.html` locally in a browser for UI review. For full flow you must run a server implementing the API endpoints the client expects (same-origin or set `API_BASE` to the server origin in `index.html`).

2. If you have an example server (Node/Express) included:
   - `cd examples/node-server`
   - `npm install`
   - Configure environment variables (see section Server Configuration).
   - `npm start`
   - Edit `index.html` and set `API_BASE = 'http://localhost:3000'` (or set CORS appropriately on the server).
   - Open `index.html` in the browser and exercise sign-up/sign-in, post creation and upload flows.

Server API contract (required endpoints)
----------------------------------------
The client expects the server to expose these REST endpoints. Implementations must honor authentication and return the specified shapes.

1) Authentication
- `POST /api/auth/signin`
  - Request: JSON { name, role } or { name, role, pass } for admin sign-in.
  - Response 200: JSON { token: "<JWT>", user: { id, name, role } }

- `POST /api/auth/signup`
  - Request: JSON { name, role } or { name, role, pass } (for admin creation if allowed).
  - Response 200: JSON { token: "<JWT>", user: { id, name, role } }

2) Posts
- `GET /api/posts`
  - Response 200: JSON { posts: [ { id, title, html, author, authorId, time (ms epoch), attachments: [{ name, key, type }] , replies: [{ id, author, text, time, replyTo? }] } ] }

- `GET /api/posts/:id`
  - Response 200: JSON post object (single)

- `POST /api/posts`
  - Auth required (admin)
  - Request: JSON { title, html, attachments: [{ name, key, type }] }
  - Response 200: JSON created post

- `DELETE /api/posts/:id`
  - Auth required (admin)
  - Response 204 or 200

3) Replies
- `POST /api/posts/:id/replies`
  - Auth required
  - Request: JSON { text, replyTo? }
  - Response 200: JSON reply object

- `DELETE /api/posts/:id/replies/:rid`
  - Auth required (admin)
  - Response 204

4) Presign upload (PUT) URL
- `POST /api/upload-url`
  - Auth required (admin)
  - Request: JSON { filename, contentType, expiresSeconds }
  - Response 200: JSON { uploadUrl: "<url to PUT to>", key: "<object key>" }
  - Notes: server should generate an object key (unique path) and return a pre-signed PUT URL. The client then performs an HTTP PUT to `uploadUrl` with `Content-Type: contentType`.

5) Presign download (GET) URL
- `POST /api/download-url`
  - Auth optional (if public listing allowed) but recommended to require auth for private objects
  - Request: JSON { key, expiresSeconds }
  - Response 200: JSON { url: "<presigned GET url>" }

6) Admin actions (optional)
- `POST /api/admin/delete` — delete admin account (requires pass) — used by the client if implemented in Product Agreement.

General server behavior expectations
------------------------------------
- Authentication: server issues JWTs with appropriate expiry and signs tokens using a secure secret. JWTs are used by the client in `Authorization: Bearer <token>`.  
- Authorization: protect admin-only endpoints (upload presign, create posts, delete posts) with role checks.  
- Auditability: record who created posts, attachments, timestamps, and any remittance paperwork for Recruited Distributors.  
- Storage: server does NOT directly accept large file payloads; clients upload to storage via presigned PUT. Server stores only object keys and metadata.  
- CORS: if client and server run on different origins, configure CORS on the server to allow the client origin and the required request methods/headers (Authorization, Content-Type, etc.). For presigned PUTs, the storage endpoint must accept the client PUT (S3 PUT presigned URLs are direct to S3).

Server configuration (example variables)
----------------------------------------
- `PORT` — server port (e.g., 3000)  
- `JWT_SECRET` — secure secret used to sign tokens  
- `STORAGE_PROVIDER` — e.g., `s3` or `minio`  
- `S3_BUCKET` — storage bucket name  
- `S3_REGION` — AWS region  
- `S3_KEY`, `S3_SECRET` — credentials for server to generate presigned URLs  
- `DB_URL` — persistence for posts/users (SQLite, Postgres, etc.)  
- `COTOPIA_SUPPORT_EMAIL` — onboarding & billing contact (support@cotopia.org)

Security and compliance guidance (critical)
-------------------------------------------
Distributors must follow security best practices and program obligations. This section summarizes operational security and items you should address before distribution or deployment.

Client considerations
- Local JWT storage: The client stores JWTs in `localStorage`. This is convenient but susceptible to XSS. If you host or distribute this UI, harden it against XSS and consider `HttpOnly` cookies / server-side session approaches for production.  
- Content rendering: the client stores and renders `post.html` (admin-supplied) directly into the DOM. To avoid XSS exposure you must:
  - Sanitize HTML on server before persisting or when serving to untrusted viewers, or  
  - Use a trusted rendering context only for content created by authorized users, and/or use CSP headers to mitigate injection risk.  
- CORS: configure server and storage CORS policies to only allow trusted origins. For presigned uploads, ensure S3 CORS allows `PUT` from intended origins only.  
- Presigned URL lifetime: limit `expiresSeconds` to a short time (e.g., 5 minutes) and require server auth to request presigns. The client uses 300s by default.  
- Access controls: require authentication to request presigned GET URLs to protect private objects. Consider object key naming (not predictable) and limit listing.  
- Audit & logging: log presign requests, upload completions (by validating object existence/size), and who uploaded what. Keep audit trails for compliance.  
- Malware scanning: validate and scan uploaded content for malware/PII if exposed publicly or distributed to Customers.  
- Rate limiting: add rate limiting on presign endpoints to prevent abuse.  
- TLS: serve the client and server over HTTPS only. Presigned URLs for S3 must be HTTPS.

Operational & legal compliance
- Data protection: if processing personal data (EU/EEA/UK), implement a DPA (Exhibit E) and ensure lawful basis for processing. Document data retention and deletion policies.  
- Export controls: do not distribute the client or enable access to storage in embargoed jurisdictions or to sanctioned parties (Master Agreement Section 18).  
- Branding: follow Exhibit D (Brand Guidelines) when using Cotopia Marks. Unauthorised use is a material breach.  
- Fees & remittance: Distributor must follow Product Agreement Exhibit F and remit required initial fees for Recruited Distributors within five (5) business days. Keep records and receipts for audits.

Distribution & licensing notes (how Distributors may use/redistribute the client)
-------------------------------------------------------------------------------
- Redistribution permitted only per terms of the selected Tier in the Product Agreement. White-labeling, source access, hosting the app as a service, or bundling with other software may require Tier 4 or separate written permission.  
- If providing the client to Recruited Distributors: Distributor must ensure each Recruited Distributor executes the required agreements and that initial remittance is completed per Exhibit F. Distributor is responsible for onboarding, initial technical training, and ensuring compliance.  
- Trademark & marketing usage: conditioned on timely payments and compliance. Always route co-branded collateral through Cotopia approval workflow defined in Exhibit D.  
- Modification: Distributor shall not materially modify the Product (including this client) unless permitted in writing. Minor localization (translations, non-branding text edits) may be allowed; confirm with Cotopia if in doubt.

Recruited Distributor onboarding checklist (operational)
-------------------------------------------------------
Use this checklist when a Distributor recruits a new Recruited Distributor (see Exhibit F details):

1. Confirm authorization to recruit under Product Agreement.  
2. Provide Recruited Distributor with:
   - Master Agreement / Product Agreement templates
   - Exhibit D (Brand Guidelines)
   - Technical quick-start (this client plus sample server info)
3. Collect signed agreements:
   - Signed Master Agreement / Distributor Agreement (or confirm prior execution)
   - Signed Product Agreement for this Product (Exhibit A variant)
   - Required tax forms (W-9/W-8 or local equivalents)
   - KYC/AML docs if required
   - Completed DPA if processing personal data
4. Collect payments:
   - Admin Fee (Distributor may retain this if permitted)
   - Initial Continued License & Tech Support Fee ($79) — Distributor must remit this $79 to Cotopia within five (5) business days
5. Submit Exhibit F (Recruited Distributor Onboarding & Remittance Form) to Cotopia with:
   - Copies of signed agreements
   - Payment confirmation for the $79 remittance
6. Provision Recruited Distributor:
   - Confirm portal access once Cotopia confirms receipt and verification
   - Provide training materials and product documentation
7. Recordkeeping:
   - Maintain copies of receipts, signed docs, and verification emails for audit (minimum retention as specified in Product Agreement/audit clause)

Packaging and redistribution (developer checklist)
-------------------------------------------------
If the Distributor will package or re-distribute the client to Recruited Distributors or Customers:

- Include this README and a packaged `LICENSE` and `NOTICE` per the Master Agreement terms.  
- Add configuration placeholders in `index.html`:
  - `API_BASE` — set to the target server hostname/origin
  - Any brand-related assets — ensure use complies with Exhibit D and Product Agreement Tier.  
- Provide a minimal server example (Node/Express) to help Recruited Distributors stand up the API endpoints securely (presign generation, JWT auth, posts store).  
- Provide operational notes for S3 bucket policies/CORS, presigned URL lifetimes and keys naming conventions.  
- Include a changelog and a version string (e.g., add `<meta name="version" content="1.0.0">`) so support and audits can track releases.

Testing & verification
----------------------
- Unit / integration tests for server endpoints (auth flows, presign generation, post CRUD).  
- Manual tests:
  - Sign up as Distributor and Admin (confirm role restrictions).
  - As Admin: create a post with attachments (image and video). Confirm presign upload flow completes and post metadata persists.  
  - As non-admin Distributor: attempt admin actions and confirm HTTP 403/401 responses.  
  - Presigned GET: verify links expire after `expiresSeconds` (or fail after expiry).  
- Security tests:
  - Run an XSS audit on content rendering flows; ensure server sanitization or CSP mitigations exist.  
  - Test unauthorized presign requests (without token) are rejected.  
  - Validate server rate limits and logging.

Troubleshooting & common issues
-------------------------------
- Upload fails with network error on PUT to `uploadUrl`:
  - Check presigned URL validity (expiration) and that `Content-Type` matches expected header.  
  - Verify CORS rules on S3 allow `PUT` and include `Authorization` / `Content-Type` if required by the presign scheme.  
- 401 Unauthorized:
  - Token expired or missing; sign in again. Check server clock skew vs. token expiry.  
- Presign response missing `uploadUrl` or `key`:
  - Server did not generate correct JSON; inspect server logs and response body.  
- Images not shown in UI:
  - Presigned GET may have expired; confirm `GET` URL generation and caching logic. The client caches presigned GETs per key for the session.  
- Replica of the UI but with different branding:
  - Ensure you have written Company authorization for white-label usage (Tier 4 or explicit permission). Unauthorized rebranding may violate the Master Agreement.

Support & contacts
------------------
- First-level support: Distributor provides first-line support to its Customers per the selected Distributor Support Model.  
- Cotopia Distributor support contact: `support@cotopia.org` (billing/onboarding and Distributor portal questions).  
- For security incidents: notify Cotopia immediately per Section 14 of the Master Agreement and follow escalation in Exhibit B.

Legal and licensing reminders
-----------------------------
- This client is provided under the terms of the Master Distribution / Reseller Licensing and Distributorship Agreement and the relevant Product Agreement (Exhibit A). Redistribution, branding and permitted use must follow the Tier and trademark conditions of the Product Agreement and Master Agreement (Sections 3, 4, 5, 13).  
- Distributors must not alter Cotopia proprietary notices or claim ownership of Cotopia IP. Any request to modify source, white-label or host alternative SaaS instances must be negotiated and documented in the Product Agreement (Tier 4 or separate SOW).  
- Recordkeeping and remittance for Recruited Distributors: follow Exhibit F operational rules and remit required initial fees within five (5) business days to avoid Distributor suspension.

Packaging for distribution (recommended files)
----------------------------------------------
When packaging this client to give to Recruited Distributors, include:
- `index.html` (the client)  
- `README.md` (this file)  
- `LICENSE` or redistribution terms (per Product Agreement)  
- `EXAMPLE_SERVER/` — server example with instructions for generating presigns and JWTs  
- `EXHIBIT_F_TEMPLATE.pdf` or `.docx` — onboarding/remittance form  
- `BRAND_GUIDELINES/` — Exhibit D (if permitted) or link to Cotopia Distributor portal for assets  
- `CHANGELOG.md` — record of changes to distributed client

Sample minimal developer commands (example)
-------------------------------------------
- Start example server (if provided):
  - `cd examples/node-server`
  - `npm install`
  - `JWT_SECRET=... S3_BUCKET=... npm start`
- Open `index.html` and set `API_BASE` inside the file to `http://localhost:3000`.
- Create an admin account via UI and test upload flow.

Next steps for Distributor onboarding
-------------------------------------
- Review the Product Agreement Exhibit A and confirm Tier, Territory and Distributor Support Model selection.  
- Execute Master Agreement and Product Agreement with Cotopia and ensure Exhibit F is used for any Recruited Distributor onboarding.  
- Set up server implementation (or use Cotopia-hosted API if provided). Ensure security controls listed above are implemented.  
- Provide training and the packaged client to Recruited Distributors, run onboarding sessions, and submit Exhibit F remittances for any recruited Distributors.

Acknowledgements & versioning
----------------------------
- Author: Cotopia Distributor Engineering / Distributorships  
- Version: 1.0 (presign client + Distributor packaging guidance)  
- Last updated: October 2025
