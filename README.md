# CIAM Engineering — Learning Notes

A structured documentation of my journey transitioning into 
Customer Identity and Access Management (CIAM) engineering.

I come from a full-stack background (JavaScript/TypeScript, Node.js, React) 
and am building deep IAM specialisation on top of that foundation.

This repo documents concepts, attack vectors, debugging scenarios, 
and real-world patterns — written to demonstrate understanding, 
not just completion.

---

## Why CIAM?

CIAM sits at the intersection of security engineering and 
developer experience. Getting authentication wrong at scale 
means millions of users exposed. Getting it right means 
invisible, frictionless security. That problem space is what 
drew me here.

---

## What This Repo Contains

| Week | Topics Covered |
|------|---------------|
| Week 1 | Cookies, Sessions, Storage, CSRF, OAuth state parameter |
| Week 2 | CORS, HTTP Security Headers, TLS, Redirects *(coming soon)* |
| Week 3 | Cryptography basics, HMAC, JWT signing foundations *(coming soon)* |

---

## Week 1 — Web & Browser Security Foundations

### Cookies
- `HttpOnly` — blocks JavaScript from reading the cookie. 
  Prevents XSS-based session theft.
- `Secure` — cookie only transmitted over HTTPS. 
  Prevents network interception.
- `SameSite` — controls cross-site cookie sending behaviour:
  - `Strict` — never sent on cross-site requests
  - `Lax` — sent on top-level navigations only (GET)
  - `None` — always sent, but **requires Secure flag** or 
    browsers will reject it
- Production rule: session cookies always require 
  `HttpOnly + Secure + SameSite=Lax` minimum.

---

### Sessions
- Server creates a session ID on login → stores session data 
  in Redis → sends session ID to browser as a cookie
- On every request: browser sends cookie → server looks up 
  session ID in Redis → validates and returns data
- Redis as centralised store = multiple servers share one 
  session store → no surprise logouts when load balancer 
  routes to a different server

**Session Fixation attack:**
Attacker plants a known session ID on the victim's browser 
before login. Victim authenticates. Server links that 
session ID to the victim's identity. Attacker, who knows 
the session ID, is now authenticated as the victim.

**Fix:** Regenerate session ID immediately after 
successful login. Old ID destroyed. New ID issued.

**Auth0 dual-session model:**
Auth0 maintains two separate sessions:
1. **Auth0 session** — sits at the IdP level. Shared across 
   all apps using the same tenant. Enables SSO.
2. **Application session** — sits at the individual app level. 
   Each app manages its own logged-in state independently.

This separation allows each app to control its own session 
lifetime while Auth0 handles the shared identity layer.

---

### Storage — Where Tokens Live

| Storage Type | XSS Readable | Persists After Close | Auto-sent by Browser |
|-------------|--------------|---------------------|----------------------|
| localStorage | Yes ❌ | Yes | No |
| sessionStorage | Yes ❌ | No | No |
| HttpOnly Cookie | No ✅ | Depends on Max-Age | Yes ✅ |

**Rule:** Session tokens belong in HttpOnly cookies. 
localStorage is readable by any JavaScript on the page — 
including injected malicious scripts.

---

### CSRF — Cross-Site Request Forgery

**The attack:**
Victim is logged into bank.com. Attacker tricks them into 
visiting evil.com. evil.com silently sends a POST request 
to bank.com/transfer. Browser automatically attaches the 
bank.com session cookie. Bank processes the transfer.

**Defences:**
1. **CSRF token** — server issues a secret token per session. 
   Every state-changing request must include it. 
   Attacker cannot read it due to Same-Origin Policy.
2. **SameSite=Strict/Lax** — browser refuses to send cookie 
   on cross-site requests. First layer of defence.
3. **OAuth state parameter** — functions as a CSRF token 
   for login flows. Generated before redirect, verified 
   on callback.

---

## Tools Used
- Browser DevTools (Network + Application tabs)
- jwt.io
- Auth0 free tenant
- Postman

---

## Target Role
CIAM / IAM Engineer — Auth0, Okta, or companies building 
identity infrastructure at scale.

Current focus: Auth0 Certified Developer exam (target: Oct 2026)
