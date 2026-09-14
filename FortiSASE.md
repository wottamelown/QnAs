# FortiSASE Overview

## What is FortiSASE?

FortiSASE (Secure Access Service Edge) is a cloud-native security platform that combines:
- **Zero Trust Access** - verify every user and device before granting access
- **Inline Content Inspection** - inspect all traffic (web, email, cloud apps, SaaS)
- **DLP (Data Loss Prevention)** - prevent sensitive data exfiltration
- **Threat Protection** - advanced malware, ransomware, and APT detection

It replaces traditional perimeter security by moving protection to the cloud and securing every access point.

## Key Components

### 1. Secure Web Gateway (SWG)
- Inspects HTTP/HTTPS traffic
- Web filtering and URL categorization
- Blocks malicious content and enforces policies

### 2. Cloud Access Security Broker (CASB)
- Monitors and controls SaaS application usage
- Detects shadow IT
- Enforces data governance policies

### 3. Firewall as a Service (FWaaS)
- Network segmentation in the cloud
- VPN alternative
- Application-layer firewall protection

### 4. Secure Email Gateway (SEG)
- Email threat protection
- Phishing and malware detection
- DLP for email

## Architecture

- **Cloud-Native**: No hardware appliances required
- **Zero Trust Model**: Assumes all traffic is untrusted
- **Agent-Based**: Deploy Forticlient agent on endpoints
- **Policy-Driven**: Granular controls based on users, devices, locations, applications

## Common Interview Topics

### Security Posture
- How FortiSASE improves security vs traditional VPN
- Zero Trust principles and implementation
- Threat detection and response capabilities

### Technical Aspects
- Traffic routing and encryption
- Integration with Fortinet ecosystem (FortiGate, FortiClient)
- APIs and automation capabilities
- Logging and analytics

### Deployment Scenarios
- Remote workforce security
- Multi-cloud environments
- Branch office connectivity
- BYOD (Bring Your Own Device) support

### Business Value
- Reduced complexity vs multiple point solutions
- Cost optimization (cloud-native, pay-as-you-go)
- Improved user experience vs traditional VPN
- Compliance and regulatory requirements

## Key Advantages

✅ **Scalability** - Cloud-based, no infrastructure limits
✅ **User Experience** - Faster, better performance than VPN
✅ **Comprehensive Security** - Unified platform vs multiple tools
✅ **Visibility** - Deep insights into all traffic and user behavior
✅ **Flexibility** - Easy policy updates without hardware changes

## Common Challenges

⚠️ **Migration Complexity** - Transitioning from legacy infrastructure
⚠️ **Adoption** - User training and change management
⚠️ **Integration** - Connecting with existing security tools
⚠️ **Cost Management** - Optimizing cloud spending

## Quick Facts

- Part of **Fortinet's Zero Trust Security** platform
- Competes with: Zscaler, Palo Alto Prisma Access, Cisco Umbrella
- Supports: Windows, macOS, Linux, iOS, Android
- Deployment: Cloud-only (SaaS)

---

# FortiSASE Interview Questions & Short Answers

Since your customer already has FortiSASE implemented, the interviewer may focus on how you operate, troubleshoot, and integrate it with FortiGate rather than asking only theoretical questions.

## 1. FortiSASE Fundamentals

**Q1. What is FortiSASE?**

A: FortiSASE is a cloud-delivered security platform that provides secure Internet and application access using services such as SWG, ZTNA, FWaaS, and security inspection.

**Q2. Why would a company use FortiSASE?**

A: To secure remote users, branches, and cloud applications without forcing all traffic through a central on-premises firewall.

**Q3. Does FortiSASE replace FortiGate?**

A: Not necessarily. FortiGate can secure local networks, VLANs, servers, and VPNs, while FortiSASE provides cloud-delivered security for users and traffic destined for the Internet or SaaS applications.

**Q4. What is a FortiSASE PoP?**

A: A Point of Presence is a Fortinet cloud location where SASE security services are delivered to connected users and traffic.

**Q5. What is the difference between FortiSASE and FortiGate?**

A: FortiGate is primarily a physical or virtual security appliance deployed on-premises or in a cloud environment. FortiSASE delivers security services from the cloud.

## 2. FortiSASE Components

**Q6. What is Secure Web Gateway (SWG) in FortiSASE?**

A: SWG secures web traffic by applying URL filtering, application control, malware inspection, and other web security policies.

**Q7. What is ZTNA in FortiSASE?**

A: Zero Trust Network Access provides users with controlled access to specific private applications based on identity, device posture, and security policies.

**Q8. What is Firewall-as-a-Service (FWaaS)?**

A: A cloud-hosted firewall service that applies network security policies without requiring a physical firewall at the user's location.

**Q9. What is CASB?**

A: Cloud Access Security Broker provides visibility and security controls for cloud applications such as Microsoft 365 and other SaaS platforms.

**Q10. What is DLP in FortiSASE?**

A: Data Loss Prevention detects and helps prevent sensitive information from being transferred to unauthorized destinations.

**Q11. What is FortiClient's role in FortiSASE?**

A: FortiClient can act as the endpoint agent that connects users to FortiSASE and applies security or access controls based on the deployment design.

## 3. FortiSASE + FortiGate Integration

**Q12. How can FortiSASE integrate with an on-premises FortiGate?**

A: Depending on the deployment model, FortiSASE can integrate with FortiGate using supported secure connectivity methods, such as IPsec tunnels, to provide access to private networks or extend security services.

**Q13. Can FortiSASE protect traffic from an on-premises network?**

A: Yes. With an appropriate deployment, on-premises traffic can be forwarded to FortiSASE for cloud security inspection before reaching the Internet.

**Q14. What is the role of FortiGate when FortiSASE is already deployed?**

A: FortiGate can continue handling local firewalling, inter-VLAN traffic, server access, VPNs, routing, and branch connectivity.

**Q15. What is the difference between local Internet breakout and FortiSASE Internet access?**

A:
- Local breakout: Traffic exits directly through the local FortiGate and ISP.
- FortiSASE access: Traffic is steered to a FortiSASE PoP for cloud-based security inspection.

**Q16. Can FortiSASE provide access to internal applications behind FortiGate?**

A: Yes, through supported private-access or ZTNA deployment methods, with appropriate connectivity, identity, and access policies.

**Q17. Does FortiSASE automatically secure every FortiGate?**

A: No. Integration requires the appropriate configuration, supported connectivity, traffic steering, and security policies.

## 4. FortiSASE Deployment & Configuration

**Q18. What are the main steps to deploy FortiSASE?**

A:
1. Define users, applications, and traffic requirements.
2. Configure the FortiSASE tenant.
3. Integrate identity and endpoint management.
4. Configure security policies.
5. Configure connectivity or traffic steering.
6. Deploy FortiClient or supported connectors.
7. Test and monitor the deployment.

**Q19. How do you onboard users into FortiSASE?**

A: Integrate the identity provider, configure authentication and access policies, deploy the required endpoint agent, and verify user connectivity.

**Q20. How do you configure web filtering?**

A: Create web-filter profiles, define allowed and blocked categories or URLs, apply the profile to the relevant security policy, and test access.

**Q21. How do you implement least privilege in FortiSASE?**

A: Restrict users to only the applications, destinations, and services required for their roles.

**Q22. How do you implement SSL inspection?**

A: Configure the supported inspection profile, deploy the required trusted CA certificate to managed endpoints, apply the profile to the relevant policy, and test exclusions and compatibility.

**Q23. What should you check before enabling SSL inspection?**

A: Certificate trust, privacy requirements, application compatibility, certificate pinning, performance, and appropriate exclusions.

**Q24. How do you configure a private application for ZTNA?**

A: Define the application and its access details, configure the required private connectivity or connector, define identity and device policies, and allow only authorized users.

## 5. FortiSASE Troubleshooting

**Q25. A user cannot access the Internet through FortiSASE. What do you check?**

A: Check endpoint connectivity, FortiClient status, authentication, traffic steering, security policies, DNS, and FortiSASE logs.

**Q26. A user is connected to FortiSASE but a website is blocked. What do you check?**

A: Check the web-filter category, URL policy, application control, SSL inspection, and the relevant security logs.

**Q27. A user can access the Internet but cannot access an internal application. What do you check?**

A: Check ZTNA authorization, user identity, device posture, private connectivity, DNS, routing, and backend firewall policies.

**Q28. FortiClient shows disconnected from FortiSASE. What do you investigate?**

A: Check Internet connectivity, FortiClient service, authentication, endpoint configuration, software version, and connection logs.

**Q29. Users complain that Internet access is slow after FortiSASE deployment. What do you check?**

A: Check the selected PoP, latency, packet loss, bandwidth, traffic steering, SSL inspection overhead, and security-policy processing.

**Q30. FortiSASE is reachable, but traffic is not being inspected. Why?**

A: Traffic may be bypassing the SASE path, the steering configuration may be incorrect, or the relevant security policy may not be applied.

**Q31. A private application is unreachable through ZTNA. What is your troubleshooting sequence?**

A: Verify user authentication → device posture → ZTNA policy → connector/tunnel status → DNS → routing → backend firewall → application availability.

## 6. Security Policies & Operations

**Q32. How do you verify whether FortiSASE blocked traffic?**

A: Search the relevant traffic, web-filter, application-control, or security-event logs using the user, destination, timestamp, and action.

**Q33. What is the difference between authentication and authorization in FortiSASE?**

A: Authentication verifies who the user is. Authorization determines which applications or resources the user can access.

**Q34. How do you handle a false-positive web-filtering block?**

A: Review the category and logs, validate the destination, and create a narrowly scoped exception if the site is legitimate.

**Q35. How do you secure remote users accessing SaaS applications?**

A: Use identity-based policies, MFA, endpoint posture checks, web filtering, application control, and appropriate inspection or DLP controls.

**Q36. How do you monitor FortiSASE?**

A: Monitor user connectivity, security events, traffic logs, policy violations, endpoint status, performance, and access failures through the available management and reporting tools.

**Q37. What is the purpose of logging in FortiSASE?**

A: To troubleshoot connectivity, investigate security incidents, audit access, and verify policy enforcement.

## 7. Scenario-Based Questions

**Q38. A customer says FortiSASE is blocking Microsoft 365. What do you do?**

A: Check the affected user, destination, policy action, web-filtering and application-control logs, SSL inspection, and any relevant exceptions. Test after making a controlled policy adjustment.

**Q39. The FortiGate has Internet access, but users are not being steered to FortiSASE. What do you check?**

A: Check the traffic-steering configuration, routing, tunnel or connector status, endpoint configuration, and FortiSASE policy matching.

**Q40. A user can authenticate but cannot access a private application.**

A: Authentication alone is not sufficient. Check authorization, device posture, ZTNA policy, private connectivity, DNS, routing, and the application's availability.

**Q41. The customer wants all branch Internet traffic inspected by FortiSASE.**

A: I would verify the supported traffic-steering design, establish the required connectivity, configure security policies, test failover and performance, and confirm that traffic is actually traversing the FortiSASE PoP.

**Q42. How would you troubleshoot a FortiSASE outage?**

A: Determine the scope, check the FortiSASE service and endpoint status, verify connectivity and traffic steering, review logs, test alternate paths if available, and follow the incident escalation procedure.

## 8. Senior-Level Questions

**Q43. How would you design a secure FortiSASE policy for a customer?**

A: Start with business requirements, identify users and applications, apply least privilege, configure web and application controls, enforce identity and device checks, enable logging, and test before production.

**Q44. What is the biggest risk when implementing FortiSASE?**

A: Incorrect traffic steering or overly restrictive policies can disrupt business access. Poor identity, certificate, or private-connectivity configuration can also cause failures.

**Q45. How would you handle a FortiSASE policy change requested by a customer?**

A: Validate the business requirement, assess the security impact, test the change where possible, obtain approval, implement it with least privilege, and monitor the result.

**Q46. How do you avoid overlapping security controls between FortiGate and FortiSASE?**

A: Define clear traffic ownership and security responsibilities, document inspection points, avoid conflicting policies, and monitor performance and logs across both platforms.

**Q47. What would you check before migrating Internet security from FortiGate to FortiSASE?**

A: Traffic flows, applications, existing policies, identity integration, certificates, routing, bandwidth, compliance requirements, exclusions, logging, and rollback procedures.

## 9. Branch On-Ramp

**Q48. What is FortiSASE Branch On-Ramp?**

A: Branch On-Ramp allows branch devices, such as FortiGate or supported third-party devices, to connect to FortiSASE Security PoPs using secure connectivity, extending FortiSASE services to branch locations.

**Q49. What problem does Branch On-Ramp solve?**

A: It allows branch users and devices to use FortiSASE security services without requiring all traffic to pass through a central headquarters firewall.

**Q50. How does a FortiGate branch connect to FortiSASE Branch On-Ramp?**

A: The branch FortiGate establishes an IPsec tunnel to a FortiSASE Branch On-Ramp Security PoP. The corresponding IPsec settings must be configured on both sides.

**Q51. What is the role of BGP in Branch On-Ramp?**

A: BGP exchanges routing information between the branch FortiGate and FortiSASE, allowing each side to learn reachable networks dynamically.

**Q52. What type of BGP is supported between FortiGate branch devices and Branch On-Ramp?**

A: For FortiGate branch devices, iBGP is supported between the branch devices and the Branch On-Ramp Security PoP.

**Q53. Can third-party routers use Branch On-Ramp?**

A: Yes, supported third-party IPsec devices can connect. In the documented Branch On-Ramp design, static routing is used because BGP is not supported for third-party branch devices.

**Q54. What is the difference between Branch On-Ramp and a normal site-to-site VPN?**

A: A normal site-to-site VPN primarily connects networks. Branch On-Ramp integrates branch connectivity with FortiSASE's cloud security and SD-WAN architecture.

**Q55. What must be configured on the branch FortiGate?**

A: IPsec parameters, routing, interfaces, firewall policies, and any required SD-WAN or BGP configuration. The branch device must match the settings configured in FortiSASE.

**Q56. Can Branch On-Ramp support multiple branches?**

A: Yes. It is designed to connect branch devices to FortiSASE Security PoPs, subject to supported devices, subscriptions, and deployment limits.

**Q57. What happens if the Branch On-Ramp IPsec tunnel goes down?**

A: Traffic using that path may fail. I would check the ISP, peer reachability, IKE/IPsec settings, tunnel status, routing, and logs.

## 10. Secure Private Access (SPA)

**Q58. What is FortiSASE Secure Private Access?**

A: SPA provides identity- and device-context-based access to private applications in on-premises data centers, private clouds, or public clouds.

**Q59. What is the difference between SIA and SPA?**

A:
- SIA: Secure Internet Access protects access to Internet and web-based applications.
- SPA: Secure Private Access protects access to private corporate applications.

**Q60. What is an SPA Connector?**

A: It is the connectivity point between FortiSASE and private applications. An existing FortiGate SD-WAN hub or supported FortiGate firewall can serve as an SPA Connector.

**Q61. Can an existing FortiGate SD-WAN hub be used for SPA?**

A: Yes. FortiSASE supports integration with an existing FortiGate SD-WAN hub as an SPA Connector.

**Q62. Explain the traffic flow for a remote user accessing an internal server.**

A:

Remote User → FortiClient / Traffic Steering → FortiSASE Security PoP → SPA Policy Enforcement → IPsec Tunnel → FortiGate SPA Connector → Internal Application

The user is granted access only if the relevant identity, device, and application policies allow it.

**Q63. Does SPA provide full network access like a traditional VPN?**

A: Not by default. SPA is designed for controlled access to explicit private applications rather than automatically granting unrestricted network access.

**Q64. What is device posture in SPA?**

A: It is the security state of a device, such as compliance or security tags, used to determine whether the device should receive access.

**Q65. What happens if a device becomes non-compliant after connecting?**

A: Access can be restricted according to the configured device-posture and private-access policies.

**Q66. Can users access private applications by IP address or domain name?**

A: Yes. Supported SPA deployment methods allow access using private IP addresses or domain names, including DNS redirection where configured.

**Q67. Can users access private applications without FortiClient?**

A: Yes, for supported use cases. FortiSASE provides agentless options such as proxy-based access and bookmark-portal access for private web applications.

## 11. Branch On-Ramp + SPA Integration

These are particularly relevant when a customer uses both technologies.

**Q68. What is the relationship between Branch On-Ramp and SPA?**

A: Both can use FortiGate connectivity and BGP routing within FortiSASE. Branch On-Ramp connects branch locations, while SPA controls secure access to private applications.

**Q69. Can Branch On-Ramp and SPA use the same BGP configuration?**

A: In the FortiGate branch-device design, Branch On-Ramp and SPA share BGP configuration. Fortinet requires SPA network configuration first before deploying a Branch On-Ramp location in that design.

**Q70. What is the difference between a Branch On-Ramp device and an SPA Connector?**

A:
- Branch On-Ramp: Connects a branch location to FortiSASE.
- SPA Connector: Provides connectivity from FortiSASE to private applications or networks.

A FortiGate can participate in both roles, depending on the architecture.

**Q71. Can a branch user access an internal application through FortiSASE?**

A: Yes, if the branch has the required connectivity, the application is reachable, and the applicable SPA policies permit access.

**Q72. How would you restrict branch users to only HQ applications?**

A: Use the required hub-and-spoke routing design, restrict private-access policies to approved HQ destinations, and deny unnecessary branch-to-branch or other private traffic.

**Q73. Does Branch On-Ramp automatically provide branch-to-branch connectivity?**

A: Not necessarily. Connectivity depends on the routing design and security policies. You should explicitly control branch-to-branch access if it is not required.

## 12. SD-WAN, BGP & Routing

**Q74. What is the difference between Branch On-Ramp and SD-WAN?**

A: Branch On-Ramp provides a connectivity method into FortiSASE. SD-WAN manages WAN paths and routing decisions based on the deployment's configured design.

**Q75. What is BGP per overlay?**

A: A routing design in which BGP is established over the relevant overlay connections. FortiSASE supports BGP per overlay and BGP on loopback for applicable parts of its architecture.

**Q76. Can you mix BGP per overlay and BGP on loopback?**

A: FortiSASE's documented BGP configuration requires one routing design method for all hubs and spokes; they cannot be mixed.

**Q77. What is route advertisement?**

A: It is the process of informing neighboring routers about reachable networks.

**Q78. What happens if the FortiSASE PoP does not learn the branch LAN route?**

A: Return traffic may not reach the branch. I would check BGP neighbor status, advertised and received routes, route filters, and routing tables.

**Q79. What happens if the branch does not learn the HQ route?**

A: The branch may not know where to send traffic destined for HQ. I would verify BGP advertisements, route installation, and any static routes or filters.

**Q80. What would you check if the BGP session is down?**

A: Peer IP reachability, ASN configuration, update-source, TCP port 179, authentication, route to the peer, and BGP logs.

**Q81. What is the purpose of route filtering?**

A: To control which routes are accepted or advertised and prevent unwanted or incorrect network reachability.

**Q82. How would you prevent branch-to-branch shortcuts?**

A: Use a hub-and-spoke topology, advertise only the required routes, apply route filtering, and enforce firewall policies denying unnecessary branch-to-branch traffic.

## 13. IPsec & Traffic Steering

**Q83. What is the difference between IPsec and BGP in this architecture?**

A: IPsec provides the encrypted tunnel; BGP exchanges routing information across the connectivity.

**Q84. What is traffic steering?**

A: Directing traffic through a selected path or security service, such as a FortiSASE PoP, based on configured rules.

**Q85. What are common ways to steer traffic to FortiSASE?**

A: Depending on the use case, FortiClient agent-based steering, IPsec with routing, explicit proxy, and agentless methods can be used.

**Q86. What is the difference between agent-based and agentless access?**

A:
- Agent-based: Uses FortiClient on the endpoint.
- Agentless: Uses supported browser/proxy or portal-based access without installing the endpoint agent.

**Q87. Why might a customer use local Internet breakout?**

A: To reduce latency and avoid backhauling Internet traffic through a central location, while still applying the required local or cloud security controls.

**Q88. What would you check if traffic is bypassing FortiSASE?**

A: Check endpoint steering, routing, policy destinations, local breakout configuration, DNS behavior, and traffic logs.

## 14. Important FortiSASE Concepts You May Be Missing

**Secure Internet Access (SIA)**

Protects Internet and web-based access through FortiSASE. Know the difference between agent-based Internet Access policies and agentless SWG policies.

**Secure SaaS Access (SSA)**

Protects access to SaaS applications using cloud-application security controls, including CASB capabilities.

**ZTNA Tags & Device Posture**

Know how device compliance or security posture can influence access to private applications.

**Digital Experience Monitoring (DEM)**

Helps monitor the user experience and reachability of private applications behind SPA Connectors.

**Identity Integration**

Understand IdP integration, SAML/OAuth authentication, MFA, user groups, and how identity is used in security policies.

**Policy Types**

Know Internet Access policies, SWG policies, and Private Access policies. Each serves a different traffic and access use case.

## 15. Senior Troubleshooting Scenarios

**Q89. Branch users can access the Internet, but cannot reach an HQ server through SPA. What do you check?**

A: Check the IPsec tunnel, BGP routes, private-access policy, FortiGate firewall rules, DNS, and return routing.

**Q90. The IPsec tunnel is up, but traffic is not passing. What could be wrong?**

A: Missing routes, incorrect selectors, firewall policies, NAT, route filtering, or a return-path issue.

**Q91. BGP is established, but the application is still unreachable. Why?**

A: BGP only provides routing information. The issue may be in firewall policies, SPA authorization, DNS, application availability, or return traffic.

**Q92. A branch loses its primary WAN link. What should happen in an SD-WAN design?**

A: SD-WAN should select a healthy alternate path if one is configured and meets the relevant performance and policy requirements.

**Q93. A remote user is authenticated but cannot access an internal application.**

A: Verify device posture, SPA authorization, destination definition, private connectivity, DNS, routing, and backend firewall policies.

**Q94. The customer wants all branches to access HQ but no branch-to-branch traffic. How would you design it?**

A: Use hub-and-spoke connectivity, advertise the necessary HQ routes, avoid advertising branch prefixes to other branches, and enforce deny policies for branch-to-branch traffic.

**Q95. How would you safely troubleshoot a production FortiSASE issue?**

A: Determine the impact, review monitoring and logs, verify connectivity and routing, compare with the last known-good configuration, make controlled changes with approval, and validate service restoration.
