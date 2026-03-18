# Azure Functions – Token‑Safe Shared Connection Review

## Summary
Briefly describe what this change does and why.

---

## ✅ Client & Connection Management
- [ ] SDK clients (HttpClient, Azure SDKs, SQL, etc.) are **static or DI‑managed**
- [ ] No new clients created per invocation
- [ ] Clients are not disposed per execution

---

## ✅ Authentication & Token Handling
- [ ] Preferred: **Managed Identity + Azure.Identity**
- [ ] No access tokens stored in singleton/static objects
- [ ] If custom tokens are used:
  - [ ] Expiration time is tracked
  - [ ] Tokens refresh **before expiration** (≥5–10 min buffer)
  - [ ] Refresh logic is centralized and thread‑safe

---

## ✅ Singleton Safety
- [ ] Singleton owns the **client only**, not credentials
- [ ] No mutable auth headers on shared clients
- [ ] No per‑execution state stored in static fields

---

## ✅ Execution & Resilience
- [ ] Function logic is stateless
- [ ] Execution tolerates running longer than token lifetime
- [ ] Retry logic is idempotent and safe

---

## ✅ Scaling & Hosting
- [ ] No assumptions about single instance execution
- [ ] No reliance on singleton for distributed coordination
- [ ] Handles scale‑out and restarts safely

---

## ✅ Observability
- [ ] Token refresh events logged (no secrets)
- [ ] Auth failures (401/403) tracked separately
- [ ] Execution duration correlated with auth failures

---

### 🔍 Reviewer Litmus Test
> **If this function runs longer than the token lifetime, will it still succeed?**

If the answer isn’t **yes**, this PR needs changes.
