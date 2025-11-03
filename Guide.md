Cotopia Presign Client — Comprehensive User & Partner Guide
Version: 1.0 — Last updated: October 2025
Contact: support@cotopia.org

Purpose and scope
-----------------
This document is a complete reference and operational guide for the Cotopia Presign Client. It is written for four distinct audiences: (1) end users who simply read posts and download attachments, (2) authorized Distributors who resell or implement Cotopia Products, (3) resellers and partners who intend to sell the client as part of a managed service or packaged software offering, and (4) technical operators and integrators who will deploy, configure, or integrate the client and its supporting server. Read this guide together with the Master Distribution / Reseller Licensing and Partnership Agreement, the Product Agreement (Exhibit A) and any applicable Exhibits (B–F). For questions about onboarding, permissions, or policy exceptions contact support@cotopia.org.

Concise summary
---------------
The Cotopia Presign Client is a lightweight browser-based application that lets authorized administrators publish posts containing HTML content and attach media (images/videos). Attachments are uploaded directly from the browser to S3-style object storage using presigned PUT URLs and are accessed via short-lived presigned GET URLs. Distribution, rebranding, hosting rights, support responsibilities, and trademark use are strictly governed by the Distributor Program agreements and the Tier selected in your Product Agreement.

Audience-specific guidance
--------------------------

End Users (people who only use the app)
- Who you are: readers, collaborators, or consumers of posts and attachments. You may also post replies or comments if your account grants that ability.  
- What to expect:
  - Accounts and roles: you will either be provisioned by your Distributor or sign up according to the workflow your Distributor provides. Typical roles are “Distributor user” and “Admin” (admins create content).  
  - Private attachments: media attached to posts are delivered via time-limited links. If a file does not load, the link likely expired — refresh the post or request a new download link from your Distributor.  
  - Safety: do not include passwords, private keys, or any sensitive credentials in posts or replies. Report suspicious content or suspected account compromise to your Distributor and to Cotopia at support@cotopia.org.  
  - Support: your Distributor is the primary contact for user support under default Distributor support models. Company-level support from Cotopia is only available where a Product Agreement or SOW authorizes it.

Distributors (authorized partners reselling or supporting the product)
- Who you are: corporate entities that have executed a Product Agreement with Cotopia and are authorized to market, sell, implement, and support the Product within a defined Territory and Tier.  
- Core obligations:
  - Legal & commercial: execute the Master Agreement and Product Agreement, pay the Admin Fee and Continued License & Tech Support Fees, and comply with branding, reporting and audit obligations.  
  - Support model: choose and operate under one of the two Distributor Support Models defined in the Product Agreement—Distributor-as-Middleman (default) or Distributor-Handles-Direct. Respect the boundaries of Included Tech Support (Distributor personnel only) and be clear with Customers about who provides first-line support.  
  - Recruiting downstream partners: only recruit Recruited Distributors if authorized. Collect permitted Admin Fees, remit each Recruited Distributor’s initial $79 Continued License & Tech Support Fee to Cotopia within five business days, and submit Exhibit F with the required paperwork. Maintain accurate records to support audits.  
  - Security & operations: run secure deployments, protect user credentials and Customer data, enforce export controls and sanctions screening, and follow Cotopia’s brand guidelines. Train and maintain competent technical and support staff to service Customers.

Resellers / Managed-service vendors (companies packaging or selling the app)
- Who you are: organizations that plan to offer the client as part of a hosted offering or integrate the client into a packaged product for sale.  
- Key requirements:
  - Authorization: explicit written permission from Cotopia (Tier 4 or a negotiated amendment) is required before white‑labeling, rebranding, bundling, or operating the Product as a SaaS for third parties.  
  - Contracting: document hosting, SLAs, support levels and fees in a written SOW or Product Agreement amendment. Clearly define responsibility boundaries for security, backups, data deletion and support escalation.  
  - Compliance: preserve Cotopia IP notices, follow Exhibit D for brand usage, and do not create customer-facing statements that exceed your authorized rights. Obtain written pre-approval for material public announcements referencing Cotopia.

Technical operators / integrators (engineers and DevOps)
- Who you are: system administrators, DevOps engineers, or software integrators responsible for deploying and maintaining the server-side API and storage configuration the client depends on.  
- Primary technical tasks:
  - Deploy a secure server that implements authentication (JWT issuance), role-based authorization, presign generation for uploads (PUT) and downloads (GET), post metadata storage, and replies management.  
  - Configure and secure S3-compatible object storage (bucket policies, lifecycle rules, CORS) and ensure that presigned URLs are generated with appropriately short lifetimes and unpredictable object keys.  
  - Implement sanitization pipelines for any server-persisted HTML, malware scanning of uploaded attachments, and thorough logging/audit trails for administrative actions and presign events.

Functional overview — what the app does
--------------------------------------
- Authentication: users sign up and sign in; server returns a JWT token and user profile. The sample client stores the token in localStorage; production deployments should consider more secure session mechanisms.  
- Post composition: Admin users compose posts using HTML content and can attach media files. The client sends attachment metadata to the server and uploads binary files directly to storage via presigned PUT URLs.  
- Presigned upload flow: the client requests a presigned PUT URL from /api/upload-url; the server returns a unique object key and a presigned URL; the client performs the PUT request to storage; the server only stores object keys and metadata, not full file payloads.  
- Presigned download flow: to view or download attachments, the client requests a presigned GET URL from /api/download-url; the URL is short-lived and used by the browser to fetch the object.  
- Posts and replies: standard CRUD operations for posts (admin-restricted) and contributor replies; posts contain author, timestamp, HTML content, and an attachments array.  
- Auditing: server must record who issued presign requests, who created posts and replies, and provide traceability for uploads and remittances from Recruited Distributors.

Security and compliance — mandatory guidance
--------------------------------------------
These are mandatory precautions and contractual responsibilities that Distributors and operators must implement.

Authentication and session management
- The sample stores JWTs in localStorage. For production deployments prefer HttpOnly, Secure cookies with SameSite attributes to reduce token leakage via XSS. If localStorage remains in use, harden the app with strict Content Security Policy (CSP), input sanitization, and XSS audits. Use short JWT lifetimes and implement refresh token patterns where appropriate.

HTML content handling and XSS mitigation
- The client renders admin-supplied HTML. Always sanitize HTML server-side using an allowlist (whitelist) sanitizer or convert rich content to Markdown and render sanitized HTML. Do not trust client-side sanitization alone. Employ CSP to reduce risk.

Presigned URL security
- Keep presigned PUT and GET expirations short (recommended default <= 300 seconds). Require authenticated requests to generate presigns and apply rate limiting. Use unpredictable object keys (UUIDs, hashed prefixes) and avoid exposing bucket structure. Use HTTPS for all presigned URLs.

Storage configuration and CORS
- Configure storage CORS policies to only allow authorized browser origins and required methods (PUT for presigned uploads, GET for downloads). Disable public listing of buckets. Where possible, restrict S3 operations to server-generated presigns only.

Virus scanning and content validation
- For any uploaded attachments that will be served to Customers, run automated malware scanning and validate declared content types and sizes. Reject files that do not match allowed content types or that exceed configured size limits.

Audit logging and retention
- Log presign requests, upload completions (validate via HEAD or by S3 events), downloads, and administrative actions. Retain logs according to the audit and compliance period required by Cotopia and applicable law. Be prepared for Cotopia audits and provide requested records.

Privacy, DPA and data residency
- If personal data is processed, execute a DPA (Exhibit E) where required and ensure lawful bases for processing. Follow applicable retention and deletion requirements. Respect data locality obligations for regulated jurisdictions.

Export controls and sanctions compliance
- Do not provide access or distribute the application in embargoed or sanctioned jurisdictions or to blocked persons. Screen customers and Recruited Distributors against sanctions lists and comply with export control laws.

Operational deployment checklist
-------------------------------
A practical step-by-step checklist to get the full system running securely.

Prerequisites
- Production-grade host environment with TLS.  
- Object storage (AWS S3 or S3-compatible) with credentials for presign generation.  
- Database for posts, users and audit logs (Postgres, MySQL, or SQLite for testing).  
- Secret store for JWT signing secret and API credentials (do not commit secrets to repo).  
- Optional: virus scanning integration (ClamAV, commercial scanners), S3 event processing, and SIEM/monitoring.

Deployment steps (example)
1. Provision a secure server and apply TLS using a verified certificate (Let’s Encrypt or enterprise CA).  
2. Create an S3 bucket and configure CORS, bucket policies, lifecycle policies and versioning if required. Disable public list/browse where not needed.  
3. Configure environment variables: PORT, JWT_SECRET, S3_BUCKET, S3_REGION, S3_KEY, S3_SECRET, DB_URL, COTOPIA_SUPPORT_EMAIL = support@cotopia.org.  
4. Deploy server code implementing endpoints described below. Secure all admin endpoints and presign generation behind authentication and role checks.  
5. Configure the client’s API_BASE to point to the server origin, or serve index.html from the same origin to avoid CORS complexity.  
6. Create initial admin user(s) and test the full presign upload and download cycles including validation and auditing.  
7. Integrate malware scanning and post-upload verification. Use S3 HEAD or event-driven checks to ensure uploaded files match expected size and content type.  
8. Implement rate limiting and monitoring on presign endpoints to guard against abuse.

Server API contract (expected endpoints and behavior)
----------------------------------------------------
The client requires the following server API endpoints. Implementors must follow authentication, authorization and response shape expectations.

Authentication
- POST /api/auth/signin
  - Request: JSON { name, role } or { name, role, pass } (format may be extended for production).  
  - Response 200: JSON { token: "<JWT>", user: { id, name, role } }.

- POST /api/auth/signup
  - Request: JSON { name, role } or { name, role, pass }.  
  - Response 200: JSON { token: "<JWT>", user: { id, name, role } }.

Posts and replies
- GET /api/posts
  - Response 200: JSON { posts: [ ... ] } where each post includes id, title, html, author, authorId, time (epoch ms), attachments: [{ name, key, type }], replies: [ ... ].

- GET /api/posts/:id
  - Response 200: JSON post object.

- POST /api/posts
  - Authenticated admin only. Request: JSON { title, html, attachments: [{ name, key, type }] }. Response 200: JSON created post.

- DELETE /api/posts/:id
  - Admin only. Response 204 or 200.

- POST /api/posts/:id/replies
  - Auth required. Request: JSON { text, replyTo? }. Response 200: JSON reply object.

- DELETE /api/posts/:id/replies/:rid
  - Admin only. Response 204.

Presign endpoints
- POST /api/upload-url
  - Authenticated admin required. Request: JSON { filename, contentType, expiresSeconds }.
  - Response 200: JSON { uploadUrl: "<presigned PUT url>", key: "<object key>" }.
  - Server must generate an unpredictable key, sign PUT presign with short expiry and optionally create a database record marked “pending”.

- POST /api/download-url
  - Auth may be required (recommended) depending on object access policy. Request: JSON { key, expiresSeconds }.
  - Response 200: JSON { url: "<presigned GET url>" }.

General server expectations
- JWT issuance and validation with a secure secret; short lifetimes recommended.  
- Role enforcement that prevents non-admins from requesting upload presigns or creating posts with attachments.  
- Post creation should validate referenced keys exist in storage and optionally enforce content-type/size limits.  
- Logging and audit records for presign generation, uploads, downloads, and administrative actions.

Testing and verification
------------------------
Perform the following checks prior to production rollout:

Functional tests
- Authentication flows issue valid JWTs and role claims.  
- Presign PUT flow: client requests /api/upload-url, receives uploadUrl and key, performs PUT, and the object appears in storage.  
- Presign GET flow: client requests /api/download-url and retrieves file successfully; GET URL must fail after expiry.  
- Post lifecycle: create, read, delete posts and replies with the correct author and timestamps.

Security tests
- XSS testing on HTML rendering: ensure stored HTML cannot be used to exfiltrate tokens or perform actions.  
- Unauthorized access: unauthenticated or unprivileged requests for presigns or admin actions return 401/403.  
- Rate limiting: presign endpoint resists automated abuse patterns.  
- Malware scanning: uploads containing malicious files are detected and rejected.

Operational tests
- Simulate S3 credential rotation and ensure server continues to generate presigns correctly.  
- Verify log retention and that audit logs include username, timestamp, IP and action details.

Distribution, packaging and permitted usage
------------------------------------------
- Redistribution of the client is permitted only under the rights specifically granted in the Product Agreement Tier. Do not assume implicit permission to rebrand, rehost, or distribute source code. Tier 4 or an express written amendment is required for white‑label/OEM rights.  
- When packaging for Recruited Distributors, include this guide, a License/NOTICE file, the Exhibit F onboarding form template, example server code (with placeholders only — do not include secrets), and brand guidelines (only if permitted by Exhibit D).  
- Maintain a version metadata field in the package (e.g., meta tag in index.html) so support and audits can identify specific releases.

Recruited Distributor operational flow (what Distributors must do)
----------------------------------------------------------------
If a Distributor is authorized to recruit downstream Recruited Distributors, follow this operational flow:

1. Authorization check: confirm recruiting rights in your Product Agreement.  
2. Provide the Recruited Distributor with the Master Agreement and Product Agreement templates, Exhibit D (Brand Guidelines) if permitted, the technical quick-start package (this client and example server notes), and any required onboarding documentation.  
3. Collect signed documentation from the Recruited Distributor: Master Agreement/Distributor Agreement, Product Agreement for each Product, tax forms, KYC/AML as required, and a completed Exhibit F.  
4. Collect fees: the Recruited Distributor pays an Admin Fee (Distributor may retain this if permitted); the Distributor must collect and remit the Recruited Distributor’s initial Continued License & Tech Support Fee ($79) to Cotopia within five (5) business days of onboarding. Retain proof of remittance and include it with Exhibit F.  
5. Submit Exhibit F and supporting paperwork to Cotopia and wait for Cotopia’s confirmation before enabling portal access for the Recruited Distributor. Cotopia may suspend access if paperwork or required initial remittance is not received timely.  
6. Provide technical onboarding and training to the Recruited Distributor and maintain records for audits.

Support, escalation and SLAs
----------------------------
- First-line support: by default the Distributor is the primary support contact for its Customers. Included Tech Support from Cotopia is provided to Distributor personnel to enable them to support Customers. Cotopia will not interact directly with Customers except where expressly authorized in a Product Agreement or SOW.  
- Escalation: follow the Partner Support SLA & Escalation Matrix (Exhibit B) for Severity 1 incidents. Contact Cotopia at support@cotopia.org for critical security incidents and major escalations.  
- SLA expectations: Cotopia publishes Distributor-facing target response times (see Master Agreement Section 7.3) but these are targets, not guaranteed remedies unless set out in a Product Agreement or separate SLA.

Legal, billing and audit references
----------------------------------
- Fees and billing: Admin Fees and Continued License & Tech Support Fees are stated in the Product Agreement. Admin Fees are one-time and non-refundable unless otherwise required by law; Continued License & Tech Support Fees are charged on a 90‑day cadence and are a condition of the Distributor license and trademark use.  
- Remittance obligations for Recruited Distributors: Distributor must remit initial $79 to Cotopia and provide executed agreements within five business days. Failure to remit or provide paperwork permits Cotopia to suspend access to the Recruited Distributor.  
- Audits: Cotopia may audit Distributor records once per 12 months with 30 days’ notice. If underreporting exceeds thresholds specified in the Master Agreement, Distributor may be responsible for audit costs.  
- Trademark use: Cotopia Marks and brand assets may be used only as permitted by the Master Agreement Section 13 and Exhibit D; Cotopia may suspend or revoke trademark licenses on nonpayment or improper use.

Troubleshooting — common problems and fixes
-------------------------------------------
- PUT to uploadUrl fails with network/CORS errors:
  - Confirm the presigned URL has not expired.  
  - Ensure the Content-Type header provided in PUT matches the contentType used to generate the presign.  
  - Verify S3 bucket CORS allows PUT from your client origin and the storage endpoint is accepting the request.  
- 401 / 403 responses from API:
  - Token expiration or missing Authorization header. Re-authenticate and confirm tokens are sent as Authorization: Bearer <token>.  
- Presigned GET link succeeds initially then fails:
  - The presigned GET link has time-limited validity. Request a new presigned GET if the link expires. Check cached URL lifetimes in client.  
- Posts showing unexpected HTML behavior:
  - Verify server-side sanitization and confirm that admins who post HTML are trusted. Consider converting to restricted Markdown to reduce XSS risk.  
- Recruited Distributor onboarding not completed:
  - Confirm Exhibit F and initial $79 remittance were submitted to support@cotopia.org and provide proof of payment.

Support and contacts
--------------------
- Primary partner contact and general support: support@cotopia.org (use for onboarding, billing questions, branding approvals and escalations).  
- Distributor responsibilities: Distributors provide first-line support to Customers according to the selected support model; Cotopia provides Distributor-only Included Tech Support to authorized Distributor personnel as described in the agreements.

Appendices and optional deliverables (available on request)
-----------------------------------------------------------
The following materials are available from Cotopia upon request to support@cotopia.org:
- Appendix A: Example secure Node/Express server implementing JWT auth and presign generation for AWS S3 (production-hardened patterns included).  
- Appendix B: Fillable Exhibit F — Recruited Distributor Onboarding & Remittance Form (PDF and Word formats).  
- Appendix C: Detailed security checklist with recommended sanitizer libraries, CSP templates, and example S3 CORS policies.  
- Appendix D: Brand guidelines excerpt and co-branding request workflow.  
- Appendix E: Example DPA templates for EU/EEA/UK data processing scenarios.

Next steps and recommended actions for each audience
---------------------------------------------------
End Users
- Ask your Distributor for an account or sign-up instructions. Use the client to read posts and request attachments as needed. Report suspicious items to your Distributor and Cotopia.

Distributors
- Execute Master and Product Agreements, pay required fees, choose a Tier and a support model, and set up a secure server environment. If recruiting downstream partners, use Exhibit F and follow remittance timelines.

Resellers / Managed-service providers
- Secure express written permission for white-label or hosted resale, finalize SOWs, and implement production-grade hosting with monitoring, backups, SLAs and incident response.

Technical operators / Integrators
- Request the example server (Appendix A) if needed; implement secure presign endpoints, storage policies, sanitization and scanning pipelines, and add monitoring and rate limiting.

Final notes
-----------
This guide is intended to be practical and comprehensive but does not replace the contractual terms contained in the Master Agreement and Product Agreement. Contractual terms control in the event of conflict. For any clarification about permitted uses, branding, white‑labeling, or for distribution questions, contact support@cotopia.org.