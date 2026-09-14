# Cybersecurity Interview Preparation Guide

## Table of Contents
1. [Core Security Concepts](#core-security-concepts)
2. [Network Security](#network-security)
3. [Cryptography](#cryptography)
4. [Application Security](#application-security)
5. [Cloud & Infrastructure Security](#cloud--infrastructure-security)
6. [Incident Response & Threat Intelligence](#incident-response--threat-intelligence)
7. [Compliance & Governance](#compliance--governance)
8. [Advanced Topics](#advanced-topics)
9. [Common Interview Q&A](#common-interview-qa)

---

## Core Security Concepts

### CIA Triad
**Confidentiality, Integrity, Availability**

- **Confidentiality**: Data should only be accessible to authorized users
  - Controls: Encryption, access controls, authentication
  - Example: Encrypting customer data at rest and in transit
  
- **Integrity**: Data must not be modified without authorization
  - Controls: Digital signatures, hashing, checksums, database constraints
  - Example: Using HMAC to verify API requests haven't been tampered with
  
- **Availability**: Systems must be accessible when needed
  - Controls: Redundancy, load balancing, DDoS mitigation, disaster recovery
  - Example: Multi-region failover to ensure 99.99% uptime



### Zero Trust Architecture
**Core principle**: "Never trust, always verify"

- Every access request requires authentication and authorization
- Verify every device, user, and connection
- Least privilege access by default
- Continuous monitoring and validation

**Implementation**: MFA, device compliance checks, micro-segmentation, behavioral analytics

---

### Attack Surface & MITRE ATT&CK
**Attack Surface**: Sum of all possible vulnerabilities in a system

**Reducing it**:
- Disable unnecessary services
- Remove default credentials
- Regular vulnerability scanning
- Apply principle of least privilege

**MITRE ATT&CK Framework**: Comprehensive matrix of real-world adversary tactics and techniques
- Use it to assess your defenses against known attack patterns
- Example: MITRE identifies "Credential Dumping" (T1110) as common; implement credential guard

---


### Firewalls & IDS/IPS

**Firewalls**:
- **Stateful**: Tracks connection states (modern, default)
- **Stateless**: Examines individual packets (less common, faster)
- **Next-Gen Firewalls (NGFW)**: Application-aware, can understand protocols, block malware



**IDS (Intrusion Detection System)**: Monitors and alerts on suspicious activity
**IPS (Intrusion Prevention System)**: IDS + ability to block threats in real-time

### VPN & Encryption Protocols

**VPN (Virtual Private Network)**:
- Creates encrypted tunnel between user and server
- Hides IP address, encrypts traffic
- Common protocols: IPsec, OpenVPN, WireGuard

**TLS/SSL (Transport Layer Security)**:
- Asymmetric encryption for initial handshake (public/private keys)
- Symmetric encryption for session (faster)
- Mutual authentication via certificates


---

### DNS Security

**DNS Attacks**:
- **DNS Spoofing**: Attacker returns false DNS response

**Defenses**:
- **DNSSEC**: Cryptographically signs DNS responses
- **DNS over HTTPS (DoH)**: Encrypts DNS queries
- **DNS over TLS (DoT)**: TLS wrapper around DNS
- Rate limiting on DNS servers
- Monitoring for unusual query patterns

---

## Cryptography

### Symmetric vs Asymmetric

| Aspect | Symmetric | Asymmetric |
|---|---|---|
| Key Count | 1 (shared secret) | 2 (public + private) |
| Speed | Fast | Slow |
| Key Exchange | Hard problem | Solved via PKI |
| Use Case | Bulk encryption | Key exchange, signatures |
| Example | AES, ChaCha20 | RSA, ECC |


---

### Hashing

**Purpose**: One-way function to verify integrity and create fingerprints

**Properties of a good hash**:
- Deterministic (same input → same output)
- Pre-image resistant (can't find input from output)
- Collision resistant (can't find two inputs with same output)
- Avalanche effect (small input change → completely different output)

**Common algorithms**:
- **MD5**: BROKEN, don't use (collisions found)
- **SHA-1**: DEPRECATED for cryptography (Google found collisions in 2017)
- **SHA-256**: GOOD, use for new systems
- **SHA-3**: Latest standard
- **bcrypt/Argon2**: GOOD for password hashing (includes salt + work factor)

---

## Application Security

### OWASP Top 10 (2021)

1. **Broken Access Control** (most critical)
   - Problem: Users can access resources they shouldn't
   - Prevention: Implement least privilege, verify authorization on every request, use role-based access
   - Example: Attacker modifies URL from `/user/123/profile` to `/user/124/profile` and gains access

2. **Cryptographic Failures**
   - Problem: Sensitive data exposed due to weak encryption
   - Prevention: Use TLS for all data in transit, encrypt sensitive data at rest, use strong algorithms
   - Example: Storing passwords in plaintext or using MD5

3. **Injection**
   - SQL Injection: `SELECT * FROM users WHERE id = ' OR '1'='1`
   - Prevention: Use parameterized queries, input validation, least privilege DB user
   ```python

4. **Insecure Design**
   - Example: Building payment system without fraud detection

5. **Security Misconfiguration**
   - Example: Default admin credentials, exposed cloud storage, debug mode enabled

6. **Vulnerable Components**
   - Problem: Using libraries/frameworks with known vulnerabilities

7. **Authentication Failures**
   - Problem: Weak authentication, session hijacking, credential stuffing

8. **Software & Data Integrity Failures**
   - Problem: Insecure updates, unverified dependencies, CI/CD compromises

9. **Logging & Monitoring Failures**
   - Problem: Insufficient logging, no alerting

10. **SSRF (Server-Side Request Forgery)**
    - Problem: Attacker tricks server into making requests on their behalf

---

---

### API Security

**Authentication**:
- API Keys: Simple but risky if exposed (check git history)
- OAuth 2.0: Industry standard, delegated auth
- JWT (JSON Web Tokens): Stateless, but can't revoke immediately




## Cloud & Infrastructure Security

### Cloud Security Shared Responsibility Model

| Layer | AWS | Customer |
|---|---|---|
| Physical Infrastructure | ✓ | |
| Network | ✓ | Partial (VPC, Security Groups) |
| Storage | ✓ | Encryption |
| Database | ✓ | Access control |
| OS | ✓ | Patching (for AMIs) |
| Application | | ✓ |
| User Data | | ✓ |

**Key takeaway**: AWS secures "OF" the cloud, you secure "IN" the cloud.

---

### Container Security

**Risks**:
- Base image vulnerabilities (scan before use)
- Secrets in images (credentials in ENV vars or code)
- Privilege escalation (running as root)
- Supply chain (malicious base images)

**Best practices**:
```dockerfile
# VULNERABLE
FROM ubuntu:latest
RUN apt-get install everything
RUN echo "PASSWORD=secret123" >> /etc/config
EXPOSE 8080
CMD ["/app/run.sh"]

# SECURE
FROM ubuntu:22.04  # Specify version (base image scanning)
RUN apt-get update && apt-get install -y \
    curl git  # Only required packages
USER appuser  # Don't run as root
COPY --chown=appuser app/ /app/
EXPOSE 8080
HEALTHCHECK --interval=30s CMD curl -f http://localhost:8080/health || exit 1
CMD ["/app/run.sh"]
```

**Secrets Management**:
- Never in Dockerfile or environment
- Use: AWS Secrets Manager, HashiCorp Vault, Kubernetes Secrets
- Rotate regularly

---

### Kubernetes Security

**Network Policies**:
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```
This blocks all traffic by default (assume breach mentality).

**Pod Security**:
- Run as non-root user
- Read-only filesystem when possible
- No privileged containers
- Resource limits (prevent DoS)

**RBAC (Role-Based Access Control)**:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

---

### Infrastructure as Code (IaC) Security

**Scanning tools**: Checkov, TerraformScan, CloudFormation Guard
- Detect overly permissive rules
- Find hardcoded secrets
- Check compliance with CIS benchmarks

**Common misconfigurations**:
```terraform
# VULNERABLE: Open to internet
resource "aws_security_group" "web" {
  ingress {
    from_port   = 3306
    to_port     = 3306
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]  # Database exposed!
  }
}

# SECURE
resource "aws_security_group" "web" {
  ingress {
    from_port       = 3306
    to_port         = 3306
    protocol        = "tcp"
    security_groups = [aws_security_group.app.id]  # Only from app tier
  }
}
```

---

## Incident Response & Threat Intelligence

### Incident Response Plan (IRP)

**Phases**:

1. **Preparation**
   - Establish IR team and responsibilities
   - Create runbooks for common scenarios
   - Set up monitoring and alerting
   - Conduct tabletop exercises

2. **Detection & Analysis**
   - Detect unusual activity (logs, monitoring alerts)
   - Confirm it's a real incident
   - Determine scope and impact
   - Document timeline

3. **Containment**
   - **Short-term**: Isolate affected systems (prevent spread)
   - **Long-term**: Patch vulnerability, fix root cause
   - Example: Disconnect compromised server from network immediately

4. **Eradication**
   - Remove malware/attacker access
   - Patch vulnerabilities
   - Reset compromised credentials
   - Rebuild systems from known-good backups

5. **Recovery**
   - Restore systems to production
   - Verify data integrity
   - Monitor for re-compromise

6. **Post-Incident**
   - Conduct blameless postmortem
   - Document lessons learned
   - Update systems/processes to prevent recurrence

---

### Security Monitoring & SIEM

**SIEM (Security Information & Event Management)**: Centralized logging and correlation

**Key events to monitor**:
- Failed login attempts (brute force)
- Privilege escalation
- Unusual data access patterns
- System file changes
- Network anomalies (data exfiltration)

**Alert fatigue**: Too many alerts → security team ignores them
- Use baselines and anomaly detection
- Tune thresholds carefully
- Aggregate related events

---

### Threat Intelligence

**Intelligence Cycle**:
1. **Planning**: What threats do we care about?
2. **Collection**: Gather data from feeds, logs, external sources
3. **Analysis**: Correlate and analyze patterns
4. **Production**: Create actionable intelligence
5. **Dissemination**: Share with relevant teams
6. **Feedback**: Refine process

**Threat Feeds**:
- Botnet IPs, malware hashes (technical indicators)
- Industry reports on APTs (strategic intelligence)
- Vulnerability databases (CVE data)

**Example use case**:
- Threat intel identifies that attackers are targeting SaaS companies with specific phishing emails
- Share indicators of compromise (IOCs) with security teams
- Update email filters to detect sender patterns
- Brief employees on new social engineering tactics

---

## Compliance & Governance

### Major Standards

**GDPR (General Data Protection Regulation)**:
- Applies to anyone processing EU resident data
- Key requirements:
  - Consent before collecting data
  - Right to be forgotten
  - Data breach notification within 72 hours
  - Data Protection Impact Assessment (DPIA)
  - Data Protection Officer (if applicable)
- Penalties: Up to 4% of revenue

**HIPAA (Health Insurance Portability and Accountability Act)**:
- US healthcare data protection
- Requirements: Encryption, access controls, audit logs, breach notification
- Covered entities must conduct Risk Analysis

**PCI DSS (Payment Card Industry Data Security Standard)**:
- Protect credit card data
- 12 main requirements (network segmentation, encryption, monitoring)
- Applies if you accept/store card data
- Levels 1-4 based on transaction volume

**SOC 2 (Service Organization Control)**:
- For service providers (SaaS, cloud, outsourcing)
- Type I: Policies and procedures exist
- Type II: Policies work over time period (usually 6-12 months)
- Common criteria: Security, Availability, Processing Integrity, Confidentiality, Privacy

**ISO 27001**:
- International standard for Information Security Management
- Requires documented ISMS (Information Security Management System)
- Regular audits and continuous improvement

---

### Risk Management

**Risk = Likelihood × Impact**

**Example**:
- Vulnerability: SQL Injection in login page
- Likelihood: Medium (attackers regularly scan for this)
- Impact: Critical (database compromise)
- Risk: Medium × Critical = High priority fix

**Risk Assessment Process**:
1. Identify assets (data, systems, reputation)
2. Identify threats and vulnerabilities
3. Calculate risk
4. Prioritize remediation
5. Implement controls
6. Monitor and adjust

**Risk Treatment Options**:
- **Mitigate**: Reduce likelihood or impact (most common)
- **Accept**: Document and accept risk (with approval)
- **Avoid**: Change business to eliminate risk
- **Transfer**: Insurance or outsourcing

---

## Advanced Topics

### Adversary Tactics (MITRE ATT&CK Deep Dive)

**Example: Credential Dumping Attack Chain**

```
Initial Access (Phishing)
    ↓
Execution (Script execution)
    ↓
Persistence (Create user account)
    ↓
Defense Evasion (Disable Windows Defender)
    ↓
Credential Access (Dump LSASS process memory) ← MITRE T1110
    ↓
Lateral Movement (Use stolen credentials to access other systems)
    ↓
Discovery (Scan network, identify high-value targets)
    ↓
Collection (Exfiltrate data)
    ↓
Exfiltration (Compress and encrypt)
    ↓
Command & Control (Upload to attacker server)
```

**Detection at each phase**:
- Initial Access: Email filtering, user awareness
- Execution: Script analysis, EDR
- Persistence: Monitor user creation, file system changes
- Defense Evasion: Monitor security tool disabling
- Credential Access: Monitor LSASS access, GPU memory attempts
- Lateral Movement: Network monitoring, anomalous login patterns
- Collection: Monitor file access, database queries

---


**Threats**:

| Threat | Description | Mitigation |
|---|---|---|
| Spoofing | Attacker pretends to be user | MFA, certificate pinning |
| Tampering | Attacker modifies API request | TLS, request signing |
| Repudiation | User denies performing action | Comprehensive logging |
| Info Disclosure | Attacker reads sensitive data | Encryption in transit + at rest |
| DoS | Attacker overloads system | Rate limiting, WAF |
| Elevation | Attacker gains admin access | RBAC, least privilege |

---

### Secure Software Development Lifecycle (SSDLC)


**Tools**:
- **SAST** (Static Application Security Testing): Analyze code for vulnerabilities (SonarQube, Checkmarx)
- **DAST** (Dynamic Application Security Testing): Test running application (Burp Suite, OWASP ZAP)
- **IAST** (Interactive AST): Monitor runtime for vulnerabilities
- **Dependency Scanning**: Find vulnerable libraries (Snyk, Dependabot)

---

### Red Team vs Blue Team

**Red Team** (Attackers):
- Penetration testing, simulated attacks
- Identify vulnerabilities before real attackers do
- Try to break into systems, bypass controls
- Think like attacker (creative, adaptive)

**Blue Team** (Defenders):
- Implement controls, monitor systems
- Respond to incidents
- Patch vulnerabilities, harden systems
- Think like defender (systematic, documented)

**Collaboration**: Best organizations have red and blue teams working together
- Red team feeds intelligence to blue team
- Blue team updates systems based on red team findings
- Tabletop exercises where both teams participate

---

### Reverse Engineering & Malware Analysis

**Basic Process**:
1. **Static Analysis**: Examine code without running
   - Disassemble binary to assembly
   - Look for suspicious strings (URLs, C2 servers)
   - Check imports (Windows API calls indicate behavior)
   - Tools: IDA Pro, Ghidra, Radare2

2. **Dynamic Analysis**: Run in isolated sandbox
   - Monitor system calls, network connections
   - Watch file system changes, registry changes
   - Capture traffic
   - Tools: Wireshark, Process Monitor, Procmon

3. **Behavioral Analysis**: Understand capabilities
   - What does malware do?
   - Data exfiltration, persistence mechanism, command & control
   - Create IOCs (Indicators of Compromise)


## Interview Tips & Real-World Scenarios

### How to Answer: "Tell me about a security incident you handled"

**Structure (STAR method)**:
1. **Situation**: What was the incident? What was your role?
2. **Task**: What was the challenge or goal?
3. **Action**: What specific steps did you take?
4. **Result**: What was the outcome? What did you learn?

**Example Answer**:
> "We discovered unusual data exfiltration to an external IP. I immediately worked with the network team to block the IP while we investigated. I analyzed logs and found an employee's credentials had been compromised. We reset credentials, patched the server, and implemented EDR for better detection."

---


---

## Common Interview Q&A

### 1. Cybersecurity Fundamentals

**Q: What is cybersecurity?**

A: Cybersecurity is the practice of protecting systems, networks, applications, and data from unauthorized access, disruption, modification, or destruction.

Key objectives (CIA Triad):
- **Confidentiality**: Only authorized users can access information
- **Integrity**: Information remains accurate and unmodified
- **Availability**: Systems and data remain accessible when required

---

**Q: What is the difference between a threat, vulnerability, risk, and exploit?**

A:
- **Threat**: Anything that can cause harm (e.g., hacker, malware)
- **Vulnerability**: A weakness in a system (e.g., unpatched server)
- **Exploit**: A technique that takes advantage of a vulnerability (e.g., malicious code)
- **Risk**: The possibility and impact of a threat exploiting a vulnerability

*Example*: An unpatched web server (vulnerability) could be attacked by a hacker (threat) using an exploit, resulting in data theft (risk).

---

**Q: What is defense in depth?**

A: Defense in depth means using multiple security layers so that if one control fails, others continue protecting the organization.

Layers:
- Firewall → IPS → WAF → Endpoint protection → MFA → Network segmentation → SIEM → Backup & recovery

---

**Q: What is the principle of least privilege?**

A: Users, applications, and systems should receive only the minimum permissions required to perform their tasks.

*Example*: A network monitoring account should have read-only access instead of full administrator privileges.

---

**Q: What is Zero Trust?**

A: Zero Trust is a security model based on: **Never trust automatically; always verify.**

Continuously evaluates:
- User identity
- Device health
- Application location
- Risk level
- Requested resource

Uses: MFA, least privilege, segmentation, continuous monitoring, device posture checks.

---

### 2. Common Cyberattacks

**Q: What is phishing?**

A: Phishing is a social-engineering attack where an attacker impersonates a trusted person/organization to trick users into revealing credentials or clicking malicious links.

Controls:
- Email security gateway
- Anti-phishing protection
- MFA
- User awareness training
- URL filtering
- DMARC, DKIM, SPF
- EDR monitoring

---

**Q: What is ransomware?**

A: Ransomware is malware that encrypts/locks files and demands payment for recovery.

Attack chain:
1. Phishing or exposed vulnerability → Initial access
2. Privilege escalation
3. Lateral movement
4. Data exfiltration
5. Encryption and extortion

Controls:
- EDR/XDR
- Network segmentation
- MFA
- Patch management
- Offline/immutable backups
- Least privilege

---

**Q: What is a DDoS attack?**

A: A Distributed Denial of Service attack floods a target with massive traffic to make it unavailable.

Types:
- **Volumetric**: Consume bandwidth (UDP floods)
- **Protocol**: Exploit protocol weaknesses (SYN flood)
- **Application**: Attack application layer (HTTP flood)

Defenses:
- Rate limiting
- DDoS mitigation service
- CDN
- Firewalls and IPS
- Traffic analysis

---

**Q: What is a man-in-the-middle (MITM) attack?**

A: An attacker intercepts communication between two parties, eavesdropping or modifying data.

Defenses:
- TLS/SSL encryption
- Certificate pinning
- HSTS (HTTP Strict Transport Security)
- VPN
- MFA

---

### 3. Network & Web Security

**Q: What is SQL Injection?**

A: SQL Injection is when an attacker inserts malicious SQL code into input fields to manipulate database queries.

*Vulnerable example*: `SELECT * FROM users WHERE id = ' OR '1'='1`

Prevention:
- Use parameterized queries
- Input validation
- Least privilege DB user
- WAF rules

---

**Q: What is Cross-Site Scripting (XSS)?**

A: XSS is when an attacker injects malicious scripts into web pages viewed by other users.

Types:
- **Stored**: Malicious script saved in database
- **Reflected**: Malicious script in URL parameter
- **DOM-based**: Vulnerability in client-side JavaScript

Prevention:
- Input validation
- Output encoding
- Content Security Policy (CSP)
- HTTPOnly cookies

---

**Q: What is CORS?**

A: Cross-Origin Resource Sharing (CORS) is a mechanism that allows browsers to request resources from different origins securely.

Security considerations:
- Don't use `Access-Control-Allow-Origin: *` with credentials
- Validate allowed origins
- Limit allowed methods and headers

---

### 4. Cryptography & Hashing

**Q: When would you use symmetric vs. asymmetric encryption?**

A:
- **Symmetric**: Fast, use for bulk data encryption (e.g., AES)
- **Asymmetric**: Slow, use for key exchange and digital signatures (e.g., RSA)

Workflow: Use asymmetric to securely exchange a symmetric key, then use symmetric for all bulk data.

---

**Q: Why shouldn't you hash passwords with SHA-256 alone?**

A: SHA-256 is fast and reversible via brute force attacks.

Instead, use **bcrypt or Argon2** because:
- Slow (prevents brute force)
- Include salt (prevents rainbow tables)
- Adaptable work factor (can increase cost as computers get faster)

---

**Q: What is a digital signature?**

A: A digital signature proves that a message is authentic and hasn't been modified.

Process:
1. Hash the message
2. Encrypt hash with private key → signature
3. Receiver decrypts with public key and verifies

---

### 5. Access Control & Authentication

**Q: What's the difference between authentication and authorization?**

A:
- **Authentication (Authn)**: Verifies who you are (e.g., password login)
- **Authorization (Authz)**: Determines what you can access (e.g., folder permissions)

---

**Q: What is MFA?**

A: Multi-Factor Authentication (MFA) requires multiple verification methods to prove identity.

Factors:
- Something you know (password)
- Something you have (phone, security key)
- Something you are (biometric)

---

**Q: What is OAuth 2.0?**

A: OAuth 2.0 is an industry-standard protocol for delegated authentication and authorization.

Flow:
1. User clicks "Login with Google"
2. User authenticates to Google
3. Google issues token to application
4. Application uses token to access user data

---

**Q: What are common password attack methods?**

A:
- **Brute force**: Try all possible passwords
- **Dictionary attack**: Try common words
- **Rainbow table**: Pre-computed hash tables
- **Credential stuffing**: Use leaked credentials

Defense: MFA, rate limiting, strong hashing (bcrypt/Argon2)

---

### 6. Cloud & Infrastructure

**Q: What is the shared responsibility model in cloud?**

A: In cloud (e.g., AWS), security responsibilities are shared:
- **AWS secures**: Physical infrastructure, network, storage, database, OS
- **Customer secures**: Application, user data, access control, encryption

---

**Q: What are common cloud misconfigurations?**

A:
- Public S3 buckets
- Security groups open to 0.0.0.0/0
- Unencrypted databases
- Default credentials
- No MFA on root account
- Over-privileged IAM roles

---

**Q: What is container security?**

A: Securing containers requires:
- Scan base images for vulnerabilities
- Don't run as root
- Never store secrets in images
- Use least-privilege container registry access
- Implement network policies

---

### 7. Security Testing & Tools

**Q: What's the difference between SAST and DAST?**

A:
- **SAST** (Static): Analyzes code without running it (finds code vulnerabilities)
- **DAST** (Dynamic): Tests running application (finds runtime vulnerabilities)

Both are needed for comprehensive testing.

---

**Q: What is a WAF?**

A: Web Application Firewall (WAF) protects web applications by:
- Filtering malicious HTTP requests
- Detecting SQL injection, XSS, CSRF
- Rate limiting
- Bot detection

---

**Q: What is SIEM?**

A: Security Information & Event Management (SIEM) provides:
- Centralized logging from all systems
- Real-time correlation and analysis
- Alert generation
- Compliance reporting

---

### 8. Incident Response

**Q: What are the phases of incident response?**

A:
1. **Preparation**: Establish team, create runbooks, set up monitoring
2. **Detection & Analysis**: Identify and confirm incident
3. **Containment**: Isolate affected systems (short-term) and patch (long-term)
4. **Eradication**: Remove malware, reset credentials, rebuild systems
5. **Recovery**: Restore systems and verify integrity
6. **Post-Incident**: Conduct postmortem, document lessons learned

---

**Q: How would you respond to a data breach?**

A:
1. Contain: Isolate affected systems
2. Investigate: Determine scope, timeline, data affected
3. Notify: Inform users and regulatory bodies (within 72 hours if GDPR)
4. Remediate: Patch vulnerability, secure accounts
5. Monitor: Watch for re-compromise
6. Improve: Update policies and security controls

---

### 9. Compliance & Risk

**Q: What is GDPR?**

A: General Data Protection Regulation (EU law) requires:
- Consent before collecting data
- Right to be forgotten
- Data breach notification within 72 hours
- Data Protection Impact Assessment (DPIA)
- Penalties: Up to 4% of revenue

---

**Q: What is risk management?**

A: Process to identify, assess, and mitigate security risks.

Formula: **Risk = Likelihood × Impact**

Options:
- **Mitigate**: Reduce likelihood or impact
- **Accept**: Document and accept
- **Avoid**: Eliminate risk
- **Transfer**: Insurance or outsourcing

---

### 10. Continuous Improvement

**Q: How do you stay updated on security threats?**

A:
- Follow OWASP, SANS, Krebs on Security
- Subscribe to CVE databases (NVD)
- Read security advisories
- Participate in CTF competitions
- Maintain lab environment for testing
- Take certifications (OSCP, CEH, CISSP)

---

## Final Reminders for Interview Success

1. **Understand principles, not just tools**: Why TLS is important > how to install OpenSSL
2. **Show trade-off thinking**: Security vs. usability, performance, cost
3. **Think like defender AND attacker**: Demonstrate both mindsets
4. **Use specific examples**: "In a project I..." beats generic answers
5. **Ask clarifying questions**: "Before I recommend a solution, tell me about your environment"
6. **Emphasize business impact**: "This vulnerability exposes 1M customers' PII"
7. **Demonstrate continuous learning**: Certifications, courses, CTF participation
8. **Discuss incident response**: How you'd handle being compromised matters more than never being compromised

---

## Quick Reference: Key Formulas & Concepts

**Annual Loss Expectancy (ALE)** = Asset Value × Exposure Factor × Annualized Rate of Occurrence
- Example: $1M database × 100% exposure × 0.01 (1% chance/year) = $10,000 ALE

**Attack Surface**:
- Minimize: Disable services, remove code, apply least privilege
- Monitor: What's left must be defended

**Defense in Depth vs. Specialization**:
- Breadth: Understand all layers (more valuable for startups)
- Depth: Expert in one area (more valuable for large orgs)

---

*Last updated: 2026*
*Good luck with your cybersecurity interview! 🔒*
