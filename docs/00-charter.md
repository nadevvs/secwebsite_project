# Project Charter
Secure Personal Website & Custom Email Infrastructure

## 1. Project Purpose
The purpose of this project is to design, deploy, and operate a secure,
lightweight personal website with a custom domain email. The project is intended
to demonstrate cybersecurity, system administration, and project management
best practices in a production-like environment.

This project also serves as a case study for academic project management
training.

---

## 2. Project Objectives
- Deploy a publicly accessible personal website over HTTPS
- Minimize attack surface using a static-first architecture
- Implement responsible disclosure mechanisms (security.txt, PGP)
- Configure reliable custom-domain email using a managed provider
- Apply structured project management with documented risks and controls

---

## 3. Scope

### In Scope
- Domain registration and DNS configuration
- VPS provisioning and operating system hardening
- Web server configuration and website deployment
- Basic dynamic components (e.g. contact form, status endpoint)
- Custom domain email using a third-party provider
- Security controls and monitoring
- Project documentation and operational runbook

### Out of Scope
- Self-hosted production email servers
- E-commerce functionality
- Large-scale user authentication systems
- Handling of regulated personal data
- High-availability or multi-region deployments

---

## 4. Deliverables
- Live website accessible over HTTPS
- Securely configured custom email addresses
- Responsible disclosure setup (security.txt, PGP)
- Project documentation repository
- Operational runbook and maintenance plan

---

## 5. Success Criteria
The project will be considered successful when:
- The website is reachable via HTTPS with valid TLS
- Only required services are publicly exposed
- Email passes basic deliverability checks (SPF, DKIM, DMARC)
- Responsible disclosure information is published
- Project documentation is complete and up to date

---

## 6. Assumptions and Constraints
- The project is executed by a single contributor
- Budget is limited to low-cost infrastructure
- The system is intended for low traffic
- Maintenance is performed manually on a periodic basis

---

## 7. Risks (High-Level)
- Risk of attack surface growth with additional features
- Risk of misconfiguration during infrastructure setup
- Risk of email deliverability issues

Mitigation strategies for these risks will be addressed during planning and
implementation phases.

---

## 8. Stakeholders
- Project Owner / Implementer: Ilya Nadzeyeu

---

Approved by: Ilya Nadzeyeu 
Date: 16.12.2025

