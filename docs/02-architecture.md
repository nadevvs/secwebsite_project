# Architecture
Secure Personal Website & Custom Email Infrastructure

## 1. Overview
This system is a static-first personal website with a minimal Python API for a few dynamic features, deployed on a VPS and fronted by Cloudflare (proxy/CDN). Custom-domain email is provided by a managed email provider for reliability and deliverability.

Primary design goal: **minimize attack surface** while still demonstrating real sysadmin skills (DNS, TLS, hardening, reverse proxy, monitoring).

---

## 2. High-Level Components

### 2.1 Public Website (static-first)
- **Static site generator:** Hugo or Astro (build outputs static HTML/CSS/JS)
- **Web server:** Nginx serves static files directly
- **TLS:** Let’s Encrypt certificate on the VPS (origin)

### 2.2 Minimal Dynamic Backend
- **Backend:** Python **FastAPI**
- **Purpose:** a few endpoints only (e.g., contact form handler, health/status)
- **Exposure:** FastAPI listens on **localhost** only (not internet-facing)
- **Reverse proxy:** Nginx forwards only selected paths to FastAPI

### 2.3 Edge / DNS
- **Cloudflare DNS** for domain control
- **Cloudflare Proxy (orange-cloud)** enabled for the website (and API if used)
  - Provides caching/DDoS absorption and hides origin IP from casual scans

### 2.4 Email
- **Managed email provider** (Google Workspace / Proton, etc.)
- DNS configured for deliverability:
  - **MX**, **SPF**, **DKIM**, **DMARC**
- Mailboxes:
  - `contact@yourdomain.com`
  - `security@yourdomain.com`

### 2.5 Observability and Ops
- Logs: Nginx + FastAPI logs (journald/logrotate)
- Optional (private only): metrics/dashboard (e.g., node_exporter + Grafana)

---

## 3. Architecture Diagram

```mermaid
flowchart TB
  U[User Browser] --> CF[Cloudflare Proxy / CDN]
  CF --> N[Nginx on VPS :443]

  N --> S[Static Site Files]
  N -->|/api/*| A[FastAPI on localhost:8000]

  A --> M[Managed Email Provider]
  M --> Inbox[Contact and Security Mailboxes]

  subgraph DNS[DNS Records in Cloudflare]
    MX[MX Records]
    SPF[SPF Record]
    DKIM[DKIM Record]
    DMARC[DMARC Record]
  end

