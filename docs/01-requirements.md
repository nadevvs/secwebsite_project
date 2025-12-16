# Requirements & Constraints
Secure Personal Website & Custom Email Infrastructure

## 1. Purpose
This document defines the functional and non-functional requirements for the
project, plus constraints and assumptions to guide design decisions and prevent
scope creep.

---

## 2. Functional Requirements

### FR-1 Domain and DNS
- The system must use a registered domain (e.g., example.com).
- DNS must be configurable to support website hosting and email (MX, SPF, DKIM, DMARC as applicable).

### FR-2 Public Website Hosting
- The system must host a public website accessible via HTTPS (port 443).
- HTTP (port 80) must redirect to HTTPS (optional but recommended).

### FR-3 Static-First Content
- The public website should primarily serve static content (pages, posts, assets).
- The site should support a simple content workflow (build + deploy).

### FR-4 Minimal Dynamic Features
- The system must support at least one dynamic feature that demonstrates ops/sysadmin capability.
  Examples (choose at least one):
  - Contact form endpoint
  - Read-only status endpoint
  - Health check endpoint (e.g., `/healthz`)
- Dynamic components shall not require a full CMS.

### FR-5 Custom Email
- The system must provide custom-domain email addresses (at minimum `contact@` and `security@`).
- Email should be provided via a managed email provider (not self-hosted in production).

### FR-6 Responsible Disclosure
- The website should publish a security contact mechanism via `/.well-known/security.txt`.
- The website should publish a public PGP key and instructions for encrypted reporting.

### FR-7 Observability (Basic)
- The system must provide basic logs for web server and dynamic components.
- The system should support basic service health visibility (uptime check and/or metrics).

### FR-8 Documentation
- The repository should include documentation describing:
  - Architecture
  - Threat model (high level)
  - Operational runbook (maintenance, backups, incident notes)
  - Testing/validation approach

---

## 3. Non-Functional Requirements

### NFR-1 Security (Attack Surface Minimization)
- Only required network services must be publicly exposed (target: 443 only; 80 optional for redirect).
- Administrative access (e.g., SSH) must use key-based authentication and be restricted (VPN or IP allowlist preferred).
- Secrets (API keys, passwords, private keys) must not be committed to the public repository.

### NFR-2 Reliability
- The website should be available 24/7, with realistic tolerance for occasional maintenance downtime.
- Backups must exist for critical configuration and content, with a documented restore procedure.

### NFR-3 Performance
- Public pages should load quickly (static-first).
- The stack should remain lightweight (small resource footprint, minimal moving parts).

### NFR-4 Maintainability
- The system should be patchable with a predictable process (regular OS and dependency updates).
- Deployment should be repeatable and documented (manual steps acceptable if documented).

### NFR-5 Usability
- The site should be easy to navigate (clear pages: About, Projects, Writeups/Blog, Contact).
- Contact method should be straightforward (email and/or form).

### NFR-6 Cost
- Monthly operating costs should remain low (target: low-cost VPS + free/low-cost DNS/email tiers if possible).

---

## 4. Constraints
- Solo project: implementation, testing, and operations handled by a single person.
- Budget constraints: avoid complex paid services unless required for reliability (email deliverability).
- Time constraints: work is delivered in 1–3 hour sprints.
- Low traffic assumption: personal portfolio traffic, not high-scale production.
- Compliance constraints: avoid collecting/storing unnecessary personal data.

---

## 5. Assumptions
- A stable internet-accessible hosting environment is available (VPS preferred).
- Domain ownership and DNS control are available.
- Managed email provider supports custom domains and DKIM/SPF/DMARC configuration.
- The project can be documented publicly without exposing sensitive information.

---


## 7. Risks / Notes
- Overengineering: adding unnecessary features increases attack surface and maintenance burden.
  Mitigation: keep public site static-first; limit dynamic endpoints; document a strict scope boundary.
