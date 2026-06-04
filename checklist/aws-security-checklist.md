# AWS Security Review Checklist

Comprehensive checklist for reviewing AWS account security posture across all major domains.
Each item includes the control objective, how to check it, and remediation guidance.

**Assessment Date:** _______________  
**Account ID:** _______________  
**Reviewer:** _______________  
**Scope:** _______________

---

## Legend

- ✅ Pass — Control is implemented correctly
- ❌ Fail — Control is missing or misconfigured
- ⚠️ Partial — Control exists but has gaps
- N/A — Not applicable to this environment

---

## 1. Identity & Access Management (IAM)

### Root Account
- [ ] Root account has MFA enabled
- [ ] Root account has no active access keys
- [ ] Root account is not used for day-to-day operations (check CloudTrail)
- [ ] Root account email is a monitored distribution list, not an individual

### Users & Credentials
- [ ] No IAM users with console access without MFA
- [ ] Access keys rotated within 90 days
- [ ] No access keys unused for 90+ days (disable or delete)
- [ ] No IAM users with both console and programmatic access unless required
- [ ] Password policy enforces minimum length (14+), complexity, and rotation

### Permissions & Roles
- [ ] No policies with `*:*` (AdministratorAccess) attached to users directly
- [ ] EC2/Lambda/ECS roles follow least privilege — no wildcard resource ARNs
- [ ] No cross-account trust relationships without explicit justification
- [ ] IAM Access Analyzer enabled and findings reviewed
- [ ] Service Control Policies (SCPs) in place if using AWS Organizations
- [ ] Permission boundaries used for delegated administration

### Privileged Access
- [ ] Break-glass accounts documented and access logged
- [ ] Privileged role assumption requires MFA
- [ ] Just-in-time access pattern used where possible

---

## 2. GuardDuty

- [ ] GuardDuty enabled in all regions (including opt-in regions in use)
- [ ] GuardDuty enabled in all member accounts (if using Organizations)
- [ ] Threat intelligence feeds configured
- [ ] Findings routed to SIEM or notification system
- [ ] High/Critical findings reviewed within SLA (define: ___ hours)
- [ ] Suppression rules documented and reviewed quarterly
- [ ] S3 protection enabled
- [ ] EKS protection enabled (if using EKS)
- [ ] RDS protection enabled
- [ ] Lambda protection enabled

---

## 3. CloudTrail

- [ ] CloudTrail enabled in all regions
- [ ] Multi-region trail configured
- [ ] Log file validation enabled
- [ ] CloudTrail logs stored in dedicated S3 bucket with restricted access
- [ ] S3 bucket has MFA delete enabled
- [ ] S3 bucket is not publicly accessible
- [ ] CloudTrail logs encrypted with KMS CMK
- [ ] CloudWatch Logs integration configured
- [ ] Alerts configured for: root login, console login without MFA, security group changes, IAM policy changes
- [ ] Log retention period meets compliance requirements (define: ___ days)

---

## 4. S3

### Account-Level Controls
- [ ] S3 Block Public Access enabled at account level
- [ ] S3 Block Public Access enabled at organization level (if using Organizations)

### Per-Bucket Review (sample high-risk buckets)
- [ ] No buckets with public ACLs
- [ ] No buckets with public bucket policies
- [ ] Encryption at rest enabled (SSE-S3 or SSE-KMS)
- [ ] Versioning enabled on buckets containing critical data
- [ ] Object lock configured for compliance-critical buckets
- [ ] Access logging enabled for sensitive buckets
- [ ] Lifecycle policies configured to expire old versions
- [ ] Cross-account access policies reviewed and justified
- [ ] Pre-signed URL expiry is reasonable (< 1 hour for sensitive resources)

---

## 5. VPC & Network

### Security Groups
- [ ] No security groups with `0.0.0.0/0` inbound on SSH (port 22)
- [ ] No security groups with `0.0.0.0/0` inbound on RDP (port 3389)
- [ ] No security groups with unrestricted inbound on sensitive ports (3306, 5432, 6379, 27017)
- [ ] Default security group has no inbound or outbound rules
- [ ] Security groups are not shared across unrelated workloads

### Network ACLs
- [ ] NACLs reviewed for overly permissive rules
- [ ] Default NACL is not used for production resources

### VPC Configuration
- [ ] VPC Flow Logs enabled for all VPCs
- [ ] Flow logs retained for appropriate period
- [ ] Internet Gateways present only in VPCs that require internet access
- [ ] NAT Gateways used instead of public IPs for outbound-only traffic
- [ ] VPC peering connections reviewed — no transitive peering shortcuts
- [ ] PrivateLink used for AWS service access where available

---

## 6. KMS

- [ ] CMKs (Customer Managed Keys) used for sensitive workloads
- [ ] Key rotation enabled on all symmetric CMKs
- [ ] Key policies follow least privilege — no `*` principals
- [ ] Keys not used in 90+ days reviewed (disable candidates)
- [ ] Cross-account key access documented and justified
- [ ] Key deletion requests have appropriate waiting period (7-30 days)
- [ ] CloudTrail logging captures all KMS API calls

---

## 7. Security Hub

- [ ] Security Hub enabled in all regions in use
- [ ] Security Hub enabled in all member accounts
- [ ] CIS AWS Foundations Benchmark standard enabled
- [ ] AWS Foundational Security Best Practices standard enabled
- [ ] Findings routed to ticketing system or SIEM
- [ ] Critical/High findings reviewed within SLA
- [ ] Suppressed findings reviewed quarterly

---

## 8. EC2

- [ ] IMDSv2 enforced on all instances (no IMDSv1)
- [ ] No instances with public IPs unless load balancer fronted
- [ ] No publicly shared AMIs containing proprietary data
- [ ] Unattached EBS volumes reviewed (cost + data exposure)
- [ ] EBS encryption enabled by default at account level
- [ ] No EBS snapshots shared publicly
- [ ] SSM Session Manager used instead of SSH where possible
- [ ] Instance profiles used — no access keys on instances

---

## 9. Lambda

- [ ] Lambda functions do not have resource-based policies allowing `*` principal
- [ ] Execution roles follow least privilege
- [ ] Environment variables do not contain plaintext secrets (use Secrets Manager)
- [ ] Function URLs (if used) have appropriate auth configured
- [ ] VPC-attached functions reviewed for unnecessary internet access
- [ ] Lambda layers reviewed for third-party dependencies

---

## 10. RDS

- [ ] No RDS instances publicly accessible
- [ ] Encryption at rest enabled
- [ ] Automated backups enabled with appropriate retention
- [ ] IAM database authentication enabled where supported
- [ ] Multi-AZ enabled for production databases
- [ ] Minor version auto-upgrade enabled
- [ ] Deletion protection enabled on production instances
- [ ] Parameter groups reviewed for insecure settings (e.g., `log_passwords`)

---

## Findings Summary

| Domain | Pass | Fail | Partial | N/A | Priority Findings |
|---|---|---|---|---|---|
| IAM | | | | | |
| GuardDuty | | | | | |
| CloudTrail | | | | | |
| S3 | | | | | |
| VPC | | | | | |
| KMS | | | | | |
| Security Hub | | | | | |
| EC2 | | | | | |
| Lambda | | | | | |
| RDS | | | | | |

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
