# Azure Security Review Checklist

Comprehensive checklist for reviewing Azure subscription and tenant security posture across all major domains.
Each item includes the control objective, how to check it, and remediation guidance.

**Assessment Date:** _______________  
**Subscription(s):** _______________  
**Tenant ID:** _______________  
**Reviewer:** _______________  
**Scope:** _______________

---

## Legend

- ✅ Pass — Control is implemented correctly
- ❌ Fail — Control is missing or misconfigured
- ⚠️ Partial — Control exists but has gaps
- N/A — Not applicable to this environment

---

## 1. Entra ID (Azure Active Directory)

### Tenant-Level Controls
- [ ] Security defaults disabled in favor of Conditional Access (not both)
- [ ] Legacy authentication protocols blocked (Basic Auth, SMTP AUTH where not required)
- [ ] External collaboration settings reviewed — guest access limited to necessary domains
- [ ] External identities reviewed — no stale guest accounts
- [ ] Cross-tenant access settings reviewed for partner/B2B connections
- [ ] Entra ID tier is appropriate (P1/P2) for features in use

### Users & Credentials
- [ ] All users have MFA registered and enforced via Conditional Access
- [ ] No users with MFA excluded from Conditional Access policies without justification
- [ ] Password protection enabled — banned password list + on-premises enforcement
- [ ] Self-service password reset (SSPR) enabled with appropriate authentication methods
- [ ] No shared accounts — every identity tied to an individual
- [ ] Guest users reviewed and expired where no longer needed (use Access Reviews)

### Privileged Identity Management (PIM)
- [ ] PIM enabled for all privileged Entra ID roles (Global Admin, Privileged Role Admin, etc.)
- [ ] PIM enabled for Azure resource roles (Owner, Contributor, User Access Administrator)
- [ ] No permanent privileged role assignments — all privileged access is eligible/time-bound
- [ ] PIM activation requires MFA
- [ ] PIM activation requires justification
- [ ] PIM alerts reviewed (too many global admins, roles not using PIM, etc.)
- [ ] Minimum number of Global Administrators (2–4 max)
- [ ] Emergency access (break-glass) accounts documented, MFA-excluded, and monitored

### Access Reviews
- [ ] Access reviews configured for privileged roles (quarterly minimum)
- [ ] Access reviews configured for guest users (semi-annual minimum)
- [ ] Access review results actioned — reviewers not rubber-stamping

---

## 2. Conditional Access

- [ ] At least one CA policy requires MFA for all users
- [ ] CA policy requires MFA for all admins on all apps
- [ ] CA policy blocks legacy authentication protocols
- [ ] CA policy requires compliant or Hybrid Azure AD joined device for sensitive apps
- [ ] CA policy restricts access from high-risk sign-in locations or anonymous IPs
- [ ] Sign-in risk policy configured (Entra ID P2) — High risk requires MFA or block
- [ ] User risk policy configured (Entra ID P2) — High risk requires password change
- [ ] Named locations defined for trusted IP ranges
- [ ] CA policies tested in Report-only mode before enforcement
- [ ] Break-glass accounts excluded from all CA policies with monitoring in place
- [ ] No users permanently excluded from MFA CA policies without compensating controls

---

## 3. Microsoft Defender for Cloud

- [ ] Defender for Cloud enabled across all subscriptions
- [ ] Defender plans enabled for services in use:
  - [ ] Defender for Servers
  - [ ] Defender for Storage
  - [ ] Defender for SQL
  - [ ] Defender for Containers
  - [ ] Defender for App Service
  - [ ] Defender for Key Vault
  - [ ] Defender for DNS
  - [ ] Defender for Resource Manager
- [ ] Secure Score reviewed and improvement actions tracked
- [ ] Security recommendations reviewed and prioritized
- [ ] Alerts routed to SIEM (Microsoft Sentinel or equivalent)
- [ ] Regulatory compliance dashboard reviewed against applicable standards
- [ ] Workflow automations configured for high-severity alerts
- [ ] Data sensitivity settings configured (if using Defender CSPM)
- [ ] Attack path analysis reviewed (Defender CSPM)

---

## 4. Microsoft Sentinel (SIEM)

- [ ] Sentinel workspace deployed and connected to all subscriptions
- [ ] Data connectors enabled for key sources:
  - [ ] Azure Activity
  - [ ] Entra ID Sign-in and Audit Logs
  - [ ] Microsoft Defender for Cloud
  - [ ] Microsoft 365 Defender
  - [ ] Azure Firewall (if in use)
  - [ ] Network Security Group Flow Logs
- [ ] Analytics rules enabled for high-signal detections
- [ ] Scheduled analytics rules reviewed for false positive rate
- [ ] Incident review process defined — who triages, what SLA
- [ ] Automation rules / playbooks configured for common alert types (e.g., phishing response)
- [ ] Watchlists used for known-good IPs, privileged accounts, sensitive resources
- [ ] Workbooks configured for operational dashboards
- [ ] Log retention meets compliance requirements
- [ ] UEBA (User and Entity Behavior Analytics) enabled if licensed

---

## 5. Azure RBAC & Identity Governance

### Role Assignments
- [ ] No standing Owner or Contributor assignments at subscription scope without justification
- [ ] No classic administrators (Co-Administrator, Service Administrator) — migrate to RBAC
- [ ] Service principals use custom roles with minimum required permissions
- [ ] Managed identities used instead of service principal credentials where possible
- [ ] System-assigned managed identities preferred over user-assigned unless sharing is required
- [ ] Role assignments reviewed quarterly — no stale assignments

### Resource Locks
- [ ] CanNotDelete or ReadOnly locks on production resource groups where appropriate
- [ ] Lock policy enforced via Azure Policy for critical resources

### Management Groups & Subscriptions
- [ ] Management group hierarchy reflects organizational structure and policy inheritance
- [ ] Subscriptions organized into appropriate management groups (Production, Dev, Sandbox)
- [ ] Root management group policies reviewed — assignments here apply everywhere

---

## 6. Azure Policy

- [ ] Policy initiative applied at management group or subscription scope for baseline controls
- [ ] Policies enforced (not just audited) for critical controls:
  - [ ] Require encryption at rest
  - [ ] Require HTTPS only on web apps and storage
  - [ ] Deny public IP on specific resource types
  - [ ] Require specific tags on resource groups and resources
  - [ ] Deny deployment to disallowed regions
- [ ] Policy compliance dashboard reviewed — non-compliant resources remediated
- [ ] Remediation tasks configured for policies supporting auto-remediation
- [ ] Deny effects reviewed — not blocking legitimate workloads

---

## 7. Azure Key Vault

- [ ] Key Vault used for all secrets, keys, and certificates — no secrets in code or config
- [ ] Key Vault firewall enabled — access restricted to known IPs or VNet
- [ ] Soft delete enabled on all Key Vaults (now default, verify legacy vaults)
- [ ] Purge protection enabled on production Key Vaults
- [ ] RBAC authorization model used (not legacy access policies where possible)
- [ ] Managed identities used to access Key Vault — no service principal credentials
- [ ] Key rotation configured for keys — automatic where supported
- [ ] Certificate expiry monitoring configured — alert before expiry
- [ ] Diagnostic logs enabled and sent to Log Analytics / Sentinel
- [ ] No unauthorized Key Vault access in audit logs
- [ ] Secrets have expiry dates set

---

## 8. Azure Networking

### Network Security Groups (NSGs)
- [ ] No NSGs allowing inbound SSH (22) or RDP (3389) from `0.0.0.0/0`
- [ ] No NSGs allowing inbound traffic from internet to internal services directly
- [ ] NSG flow logs enabled and sent to Log Analytics
- [ ] NSG rules reviewed — no overly broad allow rules
- [ ] Default deny applied — allowlist model, not blocklist

### Virtual Networks
- [ ] VNet peering reviewed — no unintended transitive connectivity
- [ ] Private Endpoints used for PaaS services (Storage, SQL, Key Vault, etc.)
- [ ] Public endpoints disabled on PaaS services where Private Endpoints are in use
- [ ] Azure Firewall or NVA deployed for centralized egress control (hub-spoke topology)
- [ ] DDoS Protection Standard enabled on production VNets (or DDoS Network Protection)
- [ ] Bastion deployed for VM management — no public IPs on VMs

### DNS
- [ ] Private DNS zones configured for Private Endpoint resolution
- [ ] DNS hijacking risk reviewed — dangling DNS entries for decommissioned resources
- [ ] External DNS reviewed for subdomain takeover risk

---

## 9. Storage Accounts

- [ ] "Allow Blob public access" disabled at account level
- [ ] "Secure transfer required" (HTTPS only) enabled on all storage accounts
- [ ] Minimum TLS version set to TLS 1.2
- [ ] Storage account firewall enabled — access restricted to known VNets or IPs
- [ ] Shared Access Signatures (SAS) reviewed — no long-lived or overly permissive SAS tokens
- [ ] Storage account keys rotated regularly — consider disabling key access entirely (use Entra ID)
- [ ] Soft delete enabled for blobs and containers
- [ ] Versioning enabled for critical data
- [ ] Diagnostic logging enabled and sent to Log Analytics
- [ ] No storage accounts with overly permissive CORS configuration

---

## 10. Compute (VMs & Containers)

### Virtual Machines
- [ ] All VMs onboarded to Defender for Cloud
- [ ] Endpoint protection installed and reporting
- [ ] OS patches applied — no critical vulnerabilities outstanding > 30 days
- [ ] Disk encryption enabled (Azure Disk Encryption or encryption at host)
- [ ] No public IPs on VMs — use Bastion or Private Endpoints
- [ ] VM extensions reviewed — no unknown or unauthorized extensions
- [ ] Just-in-time (JIT) VM access enabled for management ports
- [ ] VM identities use managed identities — no credentials on VMs

### Azure Kubernetes Service (AKS)
- [ ] AKS cluster API server not publicly accessible (private cluster)
- [ ] RBAC enabled on AKS cluster
- [ ] Entra ID integration enabled for AKS authentication
- [ ] Network policy (Calico or Azure) configured — pod-to-pod traffic restricted
- [ ] Node OS auto-upgrade configured
- [ ] Azure Policy add-on for Kubernetes enabled
- [ ] Defender for Containers enabled
- [ ] Container images scanned for vulnerabilities before deployment
- [ ] Pod security admission controls configured
- [ ] Secrets managed via Key Vault (CSI driver) — not Kubernetes secrets for sensitive values

### Azure Container Registry (ACR)
- [ ] Admin account disabled
- [ ] Public network access disabled — access via Private Endpoint
- [ ] Image vulnerability scanning enabled (Defender for Containers)
- [ ] Geo-replication reviewed for data residency requirements
- [ ] ACR Tasks reviewed for supply chain security

---

## 11. App Services & Functions

- [ ] HTTPS only enforced — HTTP redirected or disabled
- [ ] Minimum TLS version set to 1.2
- [ ] Authentication / Authorization (Easy Auth) enabled where applicable
- [ ] Remote debugging disabled on production
- [ ] Deployment slots reviewed — staging slots secured equally to production
- [ ] App settings (environment variables) do not contain plaintext secrets — use Key Vault references
- [ ] Managed identity used for App Service to access other Azure resources
- [ ] VNet integration configured for outbound traffic (no public egress where unnecessary)
- [ ] Access restrictions configured — inbound traffic from known sources only
- [ ] Diagnostic logs enabled and sent to Log Analytics
- [ ] SCM (Kudu) access restricted

---

## 12. Azure SQL & Databases

- [ ] Azure Defender for SQL enabled
- [ ] Transparent Data Encryption (TDE) enabled
- [ ] Auditing enabled and logs sent to Log Analytics
- [ ] Advanced Threat Protection enabled
- [ ] Public network access disabled — use Private Endpoint
- [ ] Entra ID authentication enabled — minimize SQL authentication
- [ ] Firewall rules reviewed — no `0.0.0.0` to `255.255.255.255` allow rules
- [ ] Connection strings do not contain credentials — use managed identity
- [ ] Long-term backup retention configured per compliance requirements
- [ ] Vulnerability assessment enabled and findings reviewed

---

## Findings Summary

| Domain | Pass | Fail | Partial | N/A | Priority Findings |
|---|---|---|---|---|---|
| Entra ID | | | | | |
| Conditional Access | | | | | |
| Defender for Cloud | | | | | |
| Microsoft Sentinel | | | | | |
| RBAC & Governance | | | | | |
| Azure Policy | | | | | |
| Key Vault | | | | | |
| Networking | | | | | |
| Storage | | | | | |
| Compute & Containers | | | | | |
| App Services | | | | | |
| Databases | | | | | |

---

## Critical Findings (Immediate Action Required)

| # | Finding | Domain | Resource | Recommended Action |
|---|---|---|---|---|
| 1 | | | | |
| 2 | | | | |
| 3 | | | | |

---

## Remediation Roadmap

See [templates/remediation-roadmap.md](../templates/remediation-roadmap.md) for the full prioritized remediation output.
