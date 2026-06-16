# Week 1 — Cookies, Sessions, Storage, CSRF

## Quick Reference

### Cookie Flags — Production Checklist
- [ ] HttpOnly — no JavaScript access
- [ ] Secure — HTTPS only
- [ ] SameSite=Lax minimum — CSRF first layer

### Session Security — Production Checklist
- [ ] Session ID regenerated after login (fixes session fixation)
- [ ] Session stored in Redis (not in-memory, not in cookie)
- [ ] Absolute expiry set
- [ ] Sliding expiry configured for active users

### CSRF Defence Checklist
- [ ] CSRF token on every state-changing request
- [ ] SameSite cookie flag set
- [ ] OAuth state parameter generated and verified on callback

## Attack Vectors Covered
| Attack | Vector | Defence |
|--------|--------|---------|
| XSS session theft | Missing HttpOnly | Add HttpOnly flag |
| Network interception | Missing Secure | Add Secure flag |
| CSRF | Cross-site request with cookie | CSRF token + SameSite |
| Session Fixation | Session ID not regenerated | Regenerate on login |
| OAuth CSRF | Missing state parameter | Generate and verify state |
