# 🗺️ Location-Based Attack Strategy (Extended)

Use this cheat sheet to know **what attacks to try** based on **which part of the app you're testing**. Ideal for bug bounty recon → exploitation flow.


##  Login / Authentication Pages
-  **Brute-force / Rate-limiting**
-  **Credential Stuffing**
-  **Session Reuse** → Reuse old session ID after logout or password reset.
- 🧠 **Session Fixation** → Can I set the session ID before login?
-  **2FA Bypass** → Missing on sensitive actions / weak validation.
-  **OAuth / SSO Misconfig** → `redirect_uri`, `state` misuse, leaking access tokens.
- **Login CSRF** → Can login be triggered cross-origin?
-  **Password Reset Token Misuse** → Reuse, predict, or escalate via token.


## Input Fields (Search Bars, Contact/Comment Forms)
-  **Reflected / Stored XSS**
-  **HTML Injection**
-  **SSRF** → Fields accepting URLs or IPs (profile pictures, webhook URLs).
-  **Command Injection** → `; ls`, `&& whoami`, blind variations.
-  **SQLi / NoSQLi**
-  **CRLF Injection** → Headers or logs (`%0d%0a`)
-  **Local File Inclusion** → `../../etc/passwd`, `/proc/self/environ`
-  **Path Traversal** → Windows and Linux encoding variations.


##  API Endpoints
-  **Broken Access Control / IDOR**
-  **Mass Assignment**
-  **Weak Token Handling** → Expired, unsigned, or `alg: none`
-  **Rate Limit Bypass** → Headers (`X-Forwarded-For`, `X-Real-IP`)
-  **Information Disclosure** → Full error messages, debug logs, stack traces
- **JWT Replay / Reuse** → Does a JWT from logout still work?
-  **GraphQL** → Introspection, Batching Abuse, Alias Injection
-  **Broken Object Level Authorization (BOLA)** → `PUT /api/user/2/profile`


## 📎 File Upload Features
-  **RCE via Upload** → `.php`, `.jsp`, `.svg` with JS
-  **File Extension Bypass** → `file.php;.jpg`, `file.ph%00p`
-  **MIME Type Bypass** → `Content-Type: image/png`, real content is PHP
-  **Stored XSS in PDFs/Images** → `svg` payloads, meta injection
- **File Storage Issues** → Public upload folder → Direct Access (Insecure)


## 🔗 URL Parameters / Query Strings
-  **Open Redirect** → `?next=https://evil.com`
-  **IDOR** → Change `user_id=1` to `user_id=2`
-  **LFI / Path Traversal** → `?path=../../secret.txt`
-  **CRLF Injection** → Response splitting via input
-  **Client-Side Template Injection** → `{{7*7}}`, `<%= 1+1 %>`

##  Messaging / Comments / Chat / Notes
-  **Stored XSS**
-  **Markdown Injection**
-  **CSRF** → Especially for message deletion/edit
-  **Sensitive Data Exposure** → PII in public comments
-  **Replay Attacks** → Re-submit same message payload with tampered metadata


##  Admin / Backend Panels
-  **Privilege Escalation** → Low user trying admin-only features
- 🔐
-  **IDOR / Missing Auth Checks** → Bypass role checks via APIs
- 🛠️ **Command Injection** → In logs, diagnostic tools
- 📊 **Stored XSS in Dashboard Widgets**
- 🎛️ **CSRF for Admin Actions** → Trigger config change, user deletion
-  **Session Reuse After Role Downgrade** → Still using admin session?


##  WebSockets / Real-time Features
-  **Token Exposure in Payloads**
- **Message Tampering**
-  **Auth Downgrade / Replay**
-  **Unvalidated Events** → Fake `admin:updateSettings`
- 📶 **Cross-Origin WebSocket Abuse**


##  Cookies & Sessions (Site-wide)
-  **Session Reuse** → Access using old session token
-  **HttpOnly / Secure Missing**
- **Cookie Scope Misconfig** → Shared between subdomains or paths
-  **Session Fixation** → Token remains constant through login
-  **Session Expiry Missing** → Does session stay forever?
- **CSRF Token Misuse** → Missing token, predictable token, or token not tied to session

##  Misc. / Common Hidden Areas

| Component                    | Try These Attacks                                      |
|-----------------------------|---------------------------------------------------------|
| Exposed JavaScript Files | API keys, endpoint discovery, client-side logic flaws   |
| 🔗 Redirect URLs            | Open redirect, URL injection                           |
| Error Messages           | Info Disclosure, Stack Traces                           |
|  Sitemap / Robots.txt     | Hidden endpoints, admin panels                         |
|  Third-party Integrations | Misconfig in SSO, webhooks, token leaks                |
|  Caching Servers / CDNs   | Stale response, cache poisoning                        |
|  Copy-Paste Functions     | Paste XSS, clipboard injection                         |


✍️ **Written by [vGod] 
github: https://github.com/vgod-sec
🔗 *A field-focused bug bounty methodology for real-world vulnerabilities.*
