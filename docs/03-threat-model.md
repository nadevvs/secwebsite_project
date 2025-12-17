# Threat Model
Secure Personal Website & Custom Email Infrastructure

## 1. Purpose & Scope

This document describes the threat model for the personal website and custom
email infrastructure project. The goal is to identify realistic security threats,
define mitigations, and explicitly document accepted risks before implementation.

This threat model focuses on:
- Public website and API exposure
- Infrastructure hosting and administration
- Email handling and responsible disclosure mechanisms

Out of scope:
- Large-scale distributed attacks beyond basic DDoS mitigation
- Full endpoint security of administrator workstations
- Compliance with regulated data standards (no regulated data handled)

---

## 2. Architecture Summary

The system consists of a static-first public website hosted on a VPS and fronted
by Cloudflare. Nginx acts as a reverse proxy and TLS terminator, forwarding limited
API requests to a Python FastAPI backend running on localhost only. Email delivery
is handled by a managed provider. Administrative access is performed via SSH using
key-based authentication.

---

## 3. Asset Inventory

### 3.1 Infrastructure & Services
- VPS operating system and host environment
- Nginx reverse proxy and configuration
- FastAPI backend application
- Managed email service integration

### 3.2 Data Assets
- Application and access logs (Nginx, API)
- System logs
- Email content (contact messages and vulnerability reports)
- Configuration files (non-secret but sensitive)

### 3.3 Identity & Access Assets (Critical)
- SSH private keys for administrative access
- Server user credentials
- Cloudflare and VPS provider account credentials
- PGP private key for security communications
- TLS private keys for HTTPS

### 3.4 Availability & Reputation Assets
- Website availability
- Email service availability
- Domain name reputation
- Email address reputation (contact@, security@)

---

## 4. Trust Boundaries

The system contains the following trust boundaries:

- Untrusted internet clients → Cloudflare edge
- Cloudflare edge → VPS (conditionally trusted, verified via TLS and configuration)
- Nginx reverse proxy → FastAPI backend (trusted only for localhost traffic)
- Backend application → managed email provider (trusted external dependency)
- Administrator → VPS (trusted only after strong authentication)

---

## 5. STRIDE Threat Analysis

### 5.1 Boundary: Internet → Cloudflare

**Threat: Denial of Service (DoS)**  
Untrusted clients may send excessive traffic to overwhelm the service.

Mitigations:
- Cloudflare proxy and basic DDoS protection
- Static-first website design
- Rate limiting for API endpoints

Risk: Mitigated; residual risk accepted.

**Threat: Spoofing / Malicious Clients**  
Requests may originate from bots or malicious actors attempting to abuse exposed endpoints.

Mitigations:
- Cloudflare filtering and proxying
- Minimal exposed API surface
- Input validation and request filtering

Risk: Mitigated.

---

### 5.2 Boundary: Cloudflare → VPS

**Threat: Origin IP Exposure (Information Disclosure)**  
Misconfiguration or bypass of Cloudflare proxy may expose the VPS origin IP.

Mitigations:
- Cloudflare proxy enabled for public records
- Firewall restricts inbound traffic to Cloudflare IP ranges
- No direct exposure of backend services

Risk: Mitigated.

**Threat: Spoofed Proxy Headers (Spoofing)**  
Incorrect trust of client-supplied headers may lead to bypass of controls.

Mitigations:
- Trust proxy headers only from Cloudflare IP ranges
- Correct Nginx real_ip configuration
- Do not trust client-provided headers

Risk: Mitigated.

**Threat: TLS Misconfiguration (Information Disclosure / Tampering)**  
Improper TLS configuration may allow interception or modification of traffic.

Mitigations:
- Cloudflare SSL mode set to Full (strict)
- Valid TLS certificate on VPS
- Secure TLS configuration

Risk: Mitigated.

---

### 5.3 Boundary: Nginx → FastAPI Backend

**Threat: Backend Exposure (Elevation of Privilege)**  
Misconfiguration may expose the backend directly to external traffic.

Mitigations:
- Backend bound to localhost only and not reachable from untrusted networks
- Firewall blocks backend ports
- Exposure verified via port scanning

Risk: Mitigated.

**Threat: Implicit Trust of Proxied Requests (Spoofing / Tampering)**  
Backend may incorrectly trust requests forwarded by the proxy.

Mitigations:
- Input validation on all endpoints
- Method and header restrictions
- No trust assumptions based on request origin

Risk: Mitigated.

**Threat: Sensitive Data Disclosure (Information Disclosure)**  
Improper routing or logging may expose sensitive content.

Mitigations:
- Avoid logging sensitive request data
- Restrict access to logs
- Explicit proxy routing

Risk: Mitigated.

**Threat: Backend Resource Exhaustion (Denial of Service)**  
Backend may be overwhelmed by excessive requests.

Mitigations:
- Rate limiting at proxy
- Request size limits
- Minimal exposed endpoints

Risk: Mitigated.

---

### 5.4 Boundary: Backend → Email Provider

**Threat: Transport Security Failure (Information Disclosure / Tampering)**  
Improper encryption when sending emails may expose or alter message content.

Mitigations:
- Enforce TLS for mail submission
- Use trusted provider endpoints
- Avoid plaintext transmission

Risk: Mitigated.

**Threat: Email Abuse and Spam (Denial of Service)**  
Attackers may abuse contact functionality to generate excessive email.

Mitigations:
- Rate limiting on contact endpoint
- Input validation and size limits
- Generic responses to clients

Risk: Partially mitigated; residual risk accepted.

**Threat: Backend Abuse via Email Integration (Elevation of Privilege)**  
Misconfiguration could allow unintended backend behavior.

Mitigations:
- Minimal privileges for email-sending logic
- Strict separation of concerns
- Defensive error handling

Risk: Mitigated.

**Threat: Email Identity Spoofing (Spoofing)**  
Attackers may spoof email identity or abuse weak authentication.

Mitigations:
- SPF, DKIM, and DMARC configuration
- Treat inbound email as untrusted
- Do not rely on sender identity alone

Risk: Mitigated.

---

### 5.5 Boundary: Administrator → VPS

**Threat: Administrative Credential Compromise (Spoofing / Elevation of Privilege)**  
Compromise of SSH keys or credentials may grant full system access.

Mitigations:
- SSH key-based authentication only
- Strong passphrase protection
- MFA on provider accounts
- Key rotation procedures

Risk: Mitigated; residual human-factor risk accepted.

**Threat: Privilege Escalation via Misconfiguration (Elevation of Privilege)**  
Misconfigured permissions may allow escalation to root.

Mitigations:
- Least privilege user setup
- Minimal sudo permissions
- OS hardening and review

Risk: Mitigated.

**Threat: Accidental Exposure During Maintenance (Information Disclosure / DoS)**  
Services may be temporarily exposed during administrative work.

Mitigations:
- Default-deny firewall rules
- Exposure verification via port scans
- Documented maintenance procedures

Risk: Partially mitigated; short-term exposure risk accepted.

**Threat: Compromised Administrator Workstation (Spoofing / Elevation of Privilege)**  
A compromised admin device may lead to indirect system compromise.

Mitigations:
- Passphrase-protected SSH keys
- Ability to revoke access quickly

Risk: Accepted.

---

## 6. Summary of Accepted Risks

The following risks are explicitly accepted:
- Residual DoS risk beyond basic Cloudflare protections
- Limited spam abuse of contact functionality
- Short-lived exposure during maintenance
- Compromise of administrator workstation (out of scope)

These risks are considered acceptable given project scope, budget, and learning
objectives.

---

## 7. Review & Maintenance

This threat model should be reviewed and updated when:
- New public endpoints are added
- Authentication or user accounts are introduced
- Infrastructure architecture changes significantly

