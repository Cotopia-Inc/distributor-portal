Overview

Below are detailed, step‑by‑step procedures tailored to you. Each section lists preconditions, exact actions, verification steps, and what to do on failure. Follow the sequence and keep records where indicated.

A — End Users (readers and light users)
Goal: Sign in, view posts, download attachments, post replies (if allowed)

Preconditions
- You have account credentials or an invitation from your Distributor.  
- You know your Distributor support contact (primary support = your Distributor; Cotopia = support@cotopia.org).

Steps
1. Access the client
  1.1. Open the URL provided by your Distributor or open the local/hosted `index.html`.  
  1.2. Confirm the page is loaded over HTTPS. If not, do not enter credentials — contact your Distributor.

2. Sign in or sign up
  2.1. Click Sign In / Sign Up.  
  2.2. Enter your name and any required fields. If provided, supply the Distributor invitation code.  
  2.3. Submit and verify you receive a success confirmation. The client stores a session token; if asked, click “remember me” only on private devices.

3. View posts
  3.1. From the home/feed, click a post title.  
  3.2. Verify the post author and timestamp. If content looks suspicious (hidden scripts, external login prompts), report immediately.  
  3.3. If the post contains images/videos, click the media thumbnail to request a presigned GET. The client will open a secure, time-limited link.

4. Download attachments
  4.1. Click download link or external icon. The client will request a presigned GET for that object.  
  4.2. If download fails:
    - Refresh the post (F5) and try again.  
    - If still failing, contact your Distributor with the post ID and exact error message. Include timestamp and your user ID.

5. Post a reply (if permitted)
  5.1. Click Reply and enter text. Do not include sensitive data (passwords, keys).  
  5.2. Submit. Confirm your reply appears with your name and time. If reply disappears or looks altered, take a screenshot and contact Distributor.

6. Security hygiene
  6.1. Use unique, strong passwords.  
  6.2. Log out on shared devices.  
  6.3. Report suspicious links, files, or behavior to your Distributor and to support@cotopia.org.

Verification & records
- Keep a screenshot of successful downloads or notifications for 7–30 days if needed. Report issues with post ID, time and browser console logs when possible.

B — Distributors (onboarding, selling, and operational obligations)
Goal: Execute agreements, set Tier, deploy or enable service, onboard customers and Recruited Distributors, remit fees, provide support

Preconditions
- Master Agreement executed or in process.  
- Decide Product Agreement Tier and Territory.  
- Billing account ready (ACH/credit card/wire).

Steps
1. Finalize agreements & payment
  1.1. Confirm Master Agreement has been executed.  
  1.2. Complete Product Agreement (Exhibit A) for each Product and initial Product Agreement Effective Date. Select Tier (1–4) and Support Model. Initial payments due: Admin Fee (Tier amount) + initial Continued License & Tech Support Fee ($79).  
  1.3. Pay combined initial amount via agreed method. Retain payment confirmation and receipt.

2. Receive partner access & materials
  2.1. After payment and signed Product Agreement, Cotopia will provide partner portal access, brand assets (if permitted), and documentation. Verify you can log in to the partner portal.  
  2.2. Download the presign client package, README and Exhibit F template.

3. Deploy or choose hosting
  Option A — Host yourself:
    3A.1. Provision secure infrastructure (see Technical Operators checklist).  
    3A.2. Deploy server and configure S3, DB, TLS, JWT.  
    3A.3. Test upload and download flows end-to-end with test admin accounts.
  Option B — Use Cotopia-hosted API (if offered):
    3B.1. Request access details from support@cotopia.org and confirm SLAs and access controls.
    3B.2. Set API_BASE in client and verify connectivity.

4. Internal training and support readiness
  4.1. Train first-line support staff on admin workflows, post management, presign failures, and remittance procedures.  
  4.2. Prepare support templates: incident intake form, severity classification, escalation steps per Exhibit B.

5. Recruited Distributor onboarding (if authorized)
  5.1. Confirm recruiting authorization in Product Agreement.  
  5.2. Provide candidate Recruited Distributor with Master/ Product Agreement templates, Exhibit D (if allowed), and technical quick-start.  
  5.3. Collect signed agreements and required tax/compliance paperwork.  
  5.4. Collect Admin Fee from Recruited Distributor (if permitted retain) and collect initial $79 Continued License fee. Remit the Recruited Distributor’s $79 to Cotopia within five (5) business days. Include signed agreements and remittance confirmation with Exhibit F submission.  
  5.5. Submit Exhibit F to Cotopia (scan/email or portal upload per Cotopia instructions). Await confirmation before enabling their portal/keys.

6. Go‑live with Customers
  6.1. Provision customer accounts, create admin users, and hand off onboarding material.  
  6.2. Conduct onboarding calls and record attendance, training completion and support contact details. Store signed acceptance of responsibilities.

7. Operations & audits
  7.1. Maintain logs: sales, MRR/ARR, remittance records, signed Exhibit F files, and KYC/AML checks.  
  7.2. Respond to Cotopia audits within 30 days; if underreporting >5% you may bear audit costs.  
  7.3. Keep renewal notices and recurring Continued License & Tech Support Fee collection aligned to 90‑day cadence.

Verification & records
- Keep copies of all agreements, receipts, Exhibit F submissions, onboarding training attendance, and support ticket records for audit retention period specified in the Master Agreement.

C — Resellers / Managed‑service sellers (packaging, hosting and selling)
Goal: Obtain permission, document offering, deploy hosted services or bundles, maintain SLA and compliance

Preconditions
- You must have explicit written permission for hosting/resale/white‑label (Tier 4 or written amendment). Do not assume rights.

Steps
1. Confirm rights and negotiate amendments
  1.1. Request written Cotopia authorization for white-labeling, hosting for resale, or source distribution. Email support@cotopia.org with proposed model and territories.  
  1.2. Negotiate and sign SOW or Product Agreement amendment specifying fees, SLAs, support responsibilities, branding allowances, and liability allocation.

2. Define your offering & pricing
  2.1. Draft service tiers for customers: Basic (UI + hosted API), Premium (SLA/uptime/backup), Add-ons (malware scanning, custom integrations).  
  2.2. Map Cotopia’s Exhibit C fees and your margin. Include any Cotopia-required white-label fees in your costing.

3. Prepare infrastructure & compliance
  3.1. Architect production-grade hosting (multi‑AZ or equivalent, backups, encryption-at-rest and in transit).  
  3.2. Implement tenant isolation if multitenant, ensure data residency obligations for each customer and update DPA if needed.  
  3.3. Integrate malware scanning (upload pipeline), logging/SIEM, monitoring and incident response.

4. Packaging and documentation
  4.1. Create packaged deliverable for customers and Recruited Distributors: client bundle, configuration guide, onboarding checklist, Exhibit F template, Terms of Service clarifying your responsibilities.  
  4.2. Ensure you do not remove or alter Cotopia IP notices unless expressly allowed.

5. Sales enablement & compliance approvals
  5.1. Produce co-branded collateral and submit to Cotopia for approval per Exhibit D workflow.  
  5.2. Train sales teams on permitted claims, support boundaries, and required remittance flows for Recruited Distributors.

6. Launch & operational support
  6.1. Run private beta with internal users; verify presign performance and client behavior under load.  
  6.2. Open for customer sign-up once Cotopia approvals and legal agreements are in place.  
  6.3. Maintain ticketing system and document escalation flow to Cotopia support@cotopia.org for issues beyond your permitted scope.

Verification & records
- Keep signed amendment/SOWs, customer contracts, SLA acknowledgement, and evidence of Cotopia approvals for all public-facing materials.

D — Technical Operators & Integrators (installation, security, testing)
Goal: Deploy secure server, configure storage, integrate client, and verify end‑to‑end functionality

Preconditions
- Environment provisioned (VMs/containers, TLS certs, S3 access) and secrets storage available (vault/KMS). You have access to development/test S3 and DB.

Steps
1. Prepare environment
  1.1. Provision server host with hardened OS and up-to-date patches.  
  1.2. Provision DB (Postgres recommended) and secure connection credentials.  
  1.3. Provision an S3-compatible bucket and create an IAM user with minimal permissions for presign generation (no broad admin rights).

2. Secret management & configuration
  2.1. Store sensitive values (JWT_SECRET, S3 keys) in a secure vault or environment variables injected at runtime. Do not commit secrets to git.  
  2.2. Set environment variables: PORT, JWT_SECRET, S3_BUCKET, S3_REGION, S3_KEY, S3_SECRET, DB_URL, COTOPIA_SUPPORT_EMAIL = support@cotopia.org.

3. Server implementation (core endpoints)
  3.1. Implement auth endpoints: /api/auth/signup and /api/auth/signin. Issue JWTs signed by JWT_SECRET with short expiry (e.g., 15–60 minutes).  
  3.2. Implement /api/upload-url:
    - Validate caller is admin.  
    - Generate secure random key: e.g., products/{productId}/uploads/{uuidv4()}{ext}.  
    - Use S3 SDK to generate presigned PUT URL with ContentType and expiresSeconds (default <=300).  
    - Return { uploadUrl, key } and record a pending DB entry.
  3.3. Implement logic to confirm upload complete:
    - After the client PUTs, either poll with HEAD or use S3 event notifications to mark the DB record as complete and trigger malware scanning.  
  3.4. Implement /api/download-url:
    - Validate requester (if private). Generate presigned GET URL short expiry and return { url }.  
  3.5. Implement /api/posts and replies endpoints with role checks. Sanitize HTML server-side before storing or render sanitized preview for untrusted viewers.

4. Storage and CORS
  4.1. Configure S3 CORS to allow only your client origin(s) and needed methods (PUT, GET, HEAD). Example allowed headers: Content-Type, Authorization.  
  4.2. Set bucket policy to deny public list operations and prevent public read unless explicitly required for specific objects.

5. Security hardening
  5.1. Implement CSP on server responses to restrict script sources, inline scripts and dangerous features.  
  5.2. Add an input sanitizer library (DOMPurify, sanitize-html) on the server to process admin content. Maintain an allowlist of safe tags and attributes.  
  5.3. Add rate limiting to /api/upload-url and /api/download-url endpoints. Use per-IP and per-user limits.  
  5.4. Integrate malware scanning (e.g., ClamAV or a hosted scanning service) in the post-upload pipeline. Quarantine bad files and notify admin.

6. Testing — functional and security
  6.1. Functional test cases:
    - Sign up, sign in, get JWT, request upload presign, PUT a test image, request download presign, display attachment.  
    - Attempt unauthorized presign creation with non-admin token and confirm 403.  
  6.2. Security test cases:
    - XSS tests: attempt to submit malicious script in post.html and verify server rejects or sanitizes it.  
    - Token theft: verify refresh token rotation and cookie-based sessions if used.  
    - Rate limit tests: spike requests to presign endpoints and confirm throttling.  
  6.3. Operational tests:
    - Simulate expired presigned GET and confirm failure.  
    - Rotate S3 credentials and ensure your presign generation adapts.

7. Monitoring, logging and alerting
  7.1. Log presign requests, upload completions, download requests, and admin actions with user ID, timestamp and originating IP.  
  7.2. Centralize logs in SIEM and set alerting for failed uploads count spikes, malware detection events, or repeated 401/403 on presign endpoints.  
  7.3. Implement backups for DB and retention policy for logs (per audit requirements).

8. Deployment and release
  8.1. Tag release with semantic version and add version meta tag in `index.html`.  
  8.2. Create release notes documenting security changes, API contract changes, and any required migration steps.  
  8.3. Roll out to staging and conduct smoke tests before production push.

Verification — acceptance checklist
- JWTs short-lived and validated.  
- Upload presigned PUT succeeds and file visible in bucket.  
- Post creation records correct attachment keys and author metadata.  
- Presigned GET works and expires after configured time.  
- HTML content sanitized and no reflected XSS.  
- Rate limits configured and tested.  
- Malware scanning integrated and quarantines infected uploads.  
- Logs captured and stored; SIEM alerts in place.

Failure handling & escalation
- If presign endpoints are returning 5xx:
  - Check server logs for SDK errors, S3 credential issues, and clock skew. Restart service after fixing credentials.  
- If bucket access denied:
  - Verify IAM user policies and bucket policy. Ensure presign signer has s3:PutObject / s3:GetObject on intended prefix.  
- If security breach suspected:
  - Isolate affected systems, rotate keys, revoke sessions, preserve logs, and notify Cotopia at support@cotopia.org immediately per Master Agreement.

E — Quick operational checklists (one‑page style for day‑to‑day)
Daily
- Verify service health, presign endpoint latencies, and error rate < threshold.  
- Scan malware queue for recent uploads flagged and resolve.  
- Confirm backups completed.

Weekly
- Review high-severity support tickets and incident trends.  
- Confirm that Exhibit F remittances (if any) were processed and recorded.

Monthly / Quarterly
- Perform audit readiness review: receipts, signed agreements, onboarding records.  
- Update dependencies and perform security patching.  
- Rotate credentials per security policy.

Recordkeeping & compliance notes
- Retain Exhibit F forms, signed agreements, remittance receipts, and audit logs for the period specified in the Master Agreement. Provide records to Cotopia on audit with 30 days’ notice.