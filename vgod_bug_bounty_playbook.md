#  Ultimate Bug Bounty Methodology – Find Real Bugs & Build Elite Skills

## Phase 1: Target Selection

- Choose in-scope targets with:
  - Wildcard scope (`*.target.com`)
  - Rich JS apps, modern APIs, mobile integrations
  - Existing valid reports (learn from them!)
- Use platforms: HackerOne, Bugcrowd, Intigriti, YesWeHack
- Prioritize:
  - New programs (less tested)
  - Programs with few reports
  - Broken business logic (harder to find, but juicy!)

🧠 Pro Tip:
Track target changes using: `notify`, `chaos`, `github-recon`, `gau` cron


## Phase 2: Recon – Find Everything, Miss Nothing

# Subdomain Enumeration (Depth + Breadth)
subfinder -d target.com
assetfinder --subs-only target.com
amass enum -passive -d target.com
github-subdomains.py -d target.com -t <token>

# Validate with HTTPX
cat subs.txt | httpx -title -tech-detect -status-code -silent > live-subs.txt

# Visual Recon (optional)
gowitness file -f live-subs.txt

# Historical URLs
cat live-subs.txt | gau >> urls.txt
cat live-subs.txt | waybackurls >> urls.txt
sort -u urls.txt > all-urls.txt

# Extract parameters
cat all-urls.txt | gf xss > xss-params.txt
cat all-urls.txt | gf ssrf > ssrf-params.txt

# JS File Discovery
cat all-urls.txt | subjs | tee jsfiles.txt

🧠 Pro Tip:
Set daily cron jobs to auto-update recon data. Review deltas weekly.


## Phase 3: JavaScript Recon – Where Bugs Are Born

# Secret Discovery
SecretFinder -i jsfiles.txt -o cli
truffleHog, Gitleaks, shhgit for secrets in GitHub leaks

# Endpoint Hunting
linkfinder.py -i jsfile.js -o cli
grep -E "fetch|axios|xhr" jsfile.js
Look for: /admin, /internal, /v1/, /graphql, /dev-api

# Firebase, config leaks, keys
grep -i 'firebase|apiKey|clientId|secret|config' jsfile.js

# Deobfuscate/minify
jsbeautifier jsfile.js > readable.js


## Phase 4: Vulnerability Discovery – Real Testing

# XSS (auto + manual)
dalfox pipe
gf xss → custom payloads
Burp → "<script>alert(1)</script>" into every input
Check all reflected params, DOM sinks, templates

# IDOR / BAC
- Burp Repeater + Autorize
- Replace user IDs, tokens, emails
- Modify Authorization headers
- Try logged-in + logged-out scenarios

# SSRF
Check for image/pdf/url fetchers
Payloads:
http://127.0.0.1
http://169.254.169.254
http://collaborator.net

# Open Redirect
Look for ?next=, ?redirect=, ?url=
Payloads: //evil.com, %2F%2Fevil.com, http:evil.com

# LFI / RFI
Test ?file=, ?path=
Payloads:
../../../../etc/passwd
php://filter/convert.base64-encode/resource=index.php

# CORS Misconfig
corsy -i live-subs.txt
Check: Access-Control-Allow-Origin: *

# Clickjacking
iframe test
Burp’s Clickjacking plugin

# Cache Poisoning
Test with headers like:
X-Forwarded-Host: evil.com
X-Host: evil.com
Try varying paths + hosts

# Host Header Injection
Inject: evil.com
Use Burp + Collaborator

# Rate Limiting & Bruteforce
- Login, OTP, reset endpoints
- Turbo Intruder + race conditions
- Check if multiple logins lockout or allow brute

# Business Logic Bugs
Test:
- Buying cheaper items by changing prices
- Changing roles in requests
- Paying 0.00
- Bypassing 2FA
- Abusing referrals or coupons

 Pro Tip:
Business logic is where many top bounties come from. Think like a user + attacker.

## Phase 5: API Attacks – High Signal, Low Noise

- Swagger UI? OpenAPI spec? Jackpot.
- JWT tokens: Try `none` alg, base64 decode
- Test PUT, PATCH, DELETE on `/users/`, `/orders/`
- Check GraphQL introspection (`POST /graphql`)
- Fuzz deeply with Postman or Burp
- Mass assignment? Try sending extra fields


## Phase 6: Mobile Recon (Optional)
- Decompile APK using jadx or apktool
- Look for hardcoded keys, base URLs
- Test backend APIs
- Observe traffic with Burp or Frida


## Phase 7: Toolstack Summary

- **Recon**: subfinder, amass, gau, httpx
- **JS/Secrets**: SecretFinder, LinkFinder, truffleHog
- **Testing**: Burp Suite, dalfox, nuclei, gf, qsreplace
- **Automation**: bash scripts + notify + cron
- **Post-exploitation**: Collaborator, Interactsh


## Phase 8: Note-Taking & Tracking

mkdir recon/target.com
touch recon/target.com/findings.md
Include:
- Target info
- URLs tested
- Params tested
- Bugs found
- Screenshots, POC

Use Obsidian, Notion, or markdown locally.


## Phase 9: Reporting

# Template:
Summary:
Found [bug type] in [endpoint] by [input manipulation]

Steps to Reproduce:
1. Go to...
2. Enter...
3. Observe...

Impact:
[Explain clearly what attacker can do]

POC:
- Screenshot / Video
- Collaborator hit
- Burp request/response


## Phase 10: Growth & Practice Habits

- Solve daily labs on PortSwigger Web Security Academy
- Reproduce 1 HackerOne report per week
- Join bug bounty communities (Discord, Telegram, Twitter)
- Follow top hackers:
  - @nahamsec
  - @0xDanielx
  - @hakluke
  - @tomnomnom
  - @arkadiyt


## Bonus: Real Hunter Mindset

✅ Think creatively: go beyond tools  
✅ Don't skip small bugs — they lead to big ones  
✅ Read source code (JS, mobile apps, GitHub)  
✅ Treat recon like intel gathering  
✅ Your best weapon is manual logic, not automation  
✅ Stay consistent — 1–2 hours every day beats 10 hours once a week  
✅ Log everything — repeat what's working  

🎯 Goal: Become so good they can’t ignore you.


📈 You don’t need to be perfect — you need to be persistent. Every bug you find sharpens your skills. Every duplicate is a proof you’re close.

Keep building. Keep testing. You'll get there — faster than you think.

