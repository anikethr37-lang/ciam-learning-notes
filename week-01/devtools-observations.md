# DevTools Observations — Week 1

## What to look for in the Application tab

### Cookies panel
- Name — session ID or token identifier
- Value — the actual session ID (opaque string, not readable)
- HttpOnly — column shows tick if set
- Secure — column shows tick if set
- SameSite — shows Strict / Lax / None
- Expires — shows Max-Age converted to datetime

### What a production session cookie looks like
- Value: random, long, unguessable string
- HttpOnly: ✓
- Secure: ✓
- SameSite: Lax or Strict
- Domain: set to your app domain only

### Red flags to look for
- HttpOnly missing → XSS risk
- Secure missing → network interception risk
- SameSite=None without Secure → browser will reject silently
- Session ID same before and after login → session fixation risk

## Auth0 Cookie Observation
Auth0 sets two cookies on login:
1. Application session cookie — your app's session
2. Auth0 session cookie — sits at Auth0 domain, enables SSO

These are visible as separate cookies under different domains
in the Application tab.
