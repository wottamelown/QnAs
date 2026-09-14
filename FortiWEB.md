# FortiWeb — Interview Preparation Notes

This document is a cleaned-up, interview-focused summary of FortiWeb (Fortinet's web application firewall), key concepts, configuration steps, and common interview questions with concise answers to help you prepare.

## Quick Overview
- FortiWeb is a Web Application Firewall (WAF) that protects web-facing applications (DMZ servers) from web vulnerabilities (OWASP Top 10), bots, and API abuse.
- It combines load balancing, security profiles (signatures, web protection, bot mitigation), and API protection features.

## Basic Configuration Workflow (high level)
1. Create a Virtual IP (VIP) / Virtual Server: an IP address that clients connect to.
2. Create a Server Pool (set of backend nodes/servers) and add real servers (nodes).
3. Configure Health Checks (ICMP, TCP, HTTP) for pool members.
4. Create/attach Security Profiles:
   - Signature profile (attack signatures, anomalies)
   - Web Protection profile (OWASP protections, XSS/SQLi blocking, parameter validation)
   - Bot and rate-limiting rules
   - SSL profile (for SSL offloading or SSL passthrough)
5. Create Policy and bind: bind the VIP/Virtual Server to the Server Pool and apply the security profiles and other policy settings (caching, URL rewriting, DoS rules).

Notes: In some load-balancers (like F5) terminology differs: FortiWeb uses Server Pool + Virtual Server; F5 uses Pool + Virtual Server/Virtual IP.

## Health Check Types
- ICMP: Simple ping to verify host is reachable.
- TCP: Confirms TCP three-way handshake and that service port is open.
- HTTP/HTTPS: Performs an HTTP request and can validate response codes, content, or custom paths (best for web services).

## Load Balancing Methods
- Round Robin
- Weighted Round Robin (higher weight receives more connections)
- Least Connections
- Ratio-based
- Persistence (Source IP, Cookie)

## Persistence (Session Persistence / Stickiness)
- Source IP persistence: client IP sticks to the same backend.
- Cookie-based persistence: FortiWeb injects or uses a cookie to keep client on same backend.

## Content-Based Routing / URL Rewriting
- FortiWeb can route requests to different pools based on URL patterns, file types, or other request attributes (e.g., /images -> image pool).
- URL rewriting can be used to modify incoming/outgoing URLs as required.

## SSL Offloading (TLS Termination)
- FortiWeb can terminate TLS (decrypt client traffic) and optionally re-encrypt to backend.
- Benefits: offloads CPU-heavy TLS work from web servers and enables deep inspection by the WAF.
- Consideration: terminating TLS on FortiWeb means decrypted data is visible there — ensure proper security and certificate management.
- Note: ACME/ZeroSSL certificates often have short lifetimes (e.g., 90 days) and require renewal automation.

## API Protection
- Enable API Gateway/Protection features.
- Use URL masking or rewrite (e.g., replace /front/api with /xyz/api) to hide implementation paths.
- Apply rate limiting, quotas, and authentication (API keys, JWT verification) to protect APIs from abuse and excessive refreshes.

## Signatures & Web Protection Profiles
- Signature profiles detect known attack patterns and block or alert on them.
- Web protection profiles apply OWASP Top 10 protections, parameter checks, file upload restrictions, and anti-CSRF/anti-XSS rules.

## DoS / Rate Limiting
- Configure global or per-virtual-server DoS protections.
- Use rate limits, connection limits, and anomaly detection to mitigate floods and abusive behavior.

---

## Interview Questions and Model Answers
Use these to practice — both short answers and brief explanations are provided.

1) What is FortiWeb and what problems does it solve?
- Short: FortiWeb is a Web Application Firewall (WAF) that protects web applications from OWASP Top 10 threats, bots, and API abuse.
- Explanation: It inspects HTTP(S) traffic, applies signatures and behavioral rules, performs content inspection, and integrates load balancing and SSL termination to protect and accelerate web services.

2) How does FortiWeb differ from a traditional network firewall?
- Short: Network firewalls operate at L3/L4 (IP, ports), while FortiWeb inspects application-layer (L7) HTTP/HTTPS traffic for application-specific threats.
- Explanation: FortiWeb understands web protocols, HTTP methods, parameters, cookies, and can block SQLi, XSS, and malicious payloads that pass network firewalls.

3) What is the difference between SSL/TLS offloading and SSL passthrough?
- Short: Offloading terminates TLS on the appliance (decrypts traffic); passthrough forwards encrypted traffic to backend servers without decryption.
- When to use: Offloading when you need inspection and to reduce backend CPU; passthrough when end-to-end encryption is required.

4) What health checks are available and when would you use each?
- ICMP: simple reachability tests.
- TCP: verify port/service is accepting connections.
- HTTP/HTTPS: verify application-level availability and correct response (recommended for web apps).

5) Explain session persistence and when it is required.
- Short: Persistence (stickiness) ensures subsequent requests from the same client go to the same backend server.
- When required: stateful applications that store session data locally, or when backend caching/session affinity is necessary.

6) How would you mitigate API abuse or brute-force attacks on an API?
- Use rate limiting per IP/API key, implement API authentication (API keys, OAuth/JWT), enable bot detection, and apply anomaly detection and IP reputation blocking.

7) How do signature profiles and positive security (whitelisting) differ?
- Signatures: detect known bad patterns (blacklist approach).
- Positive security / whitelisting: define allowed inputs/behaviors and block everything else; generally more secure but requires careful tuning to avoid blocking valid traffic.

8) What are common challenges when deploying a WAF like FortiWeb?
- False positives blocking legitimate traffic.
- SSL certificate management and key security.
- Properly tuning signatures and whitelists for dynamic web apps.
- Ensuring performance when enabling deep inspection.

9) How do you perform troubleshooting when a legitimate request is blocked?
- Check FortiWeb logs and event details to find the triggered signature or rule, reproduce the request in a test environment, adjust signature/action or create an exception or tuning rule, and then re-test.

10) How does FortiWeb integrate with load balancers like F5 or with cloud architectures?
- FortiWeb can be placed inline, behind a load balancer, or as a reverse proxy. It integrates via VIPs, health checks, and pools; when used with F5, consider where TLS terminates and how persistence and NAT are handled.

---

## Practical Tips for Interviews
- Know the difference between L3/L4 vs L7 protection and why WAFs are necessary.
- Be ready to explain SSL/TLS termination trade-offs and certificate renewal automation (ACME).
- Have a concise example of how you would set up: VIP -> Pool -> Health checks -> Signature/Web protection -> Policy binding.
- If asked about a past incident, outline: detection, investigation (logs), mitigation (tuning, exceptions, block), and long-term fix (code or config changes).

## Quick Cheat Sheet (one-liners)
- FortiWeb = WAF + LB features for web applications.
- Health checks: ICMP / TCP / HTTP.
- Persistence: source IP or cookie.
- SSL offload = decrypt on WAF; passthrough = encrypted to backend.
- API protection = rate limits + auth + URL masking.

---

## Next options
If you want, I can also:
- Create a short flashcard set from these Q&A for practice.
- Add real-world examples / topology diagrams (text ASCII) showing where FortiWeb sits relative to load balancers and web servers.
- Tailor the Q&A toward junior or senior interview levels.
