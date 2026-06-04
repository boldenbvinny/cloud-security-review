# cloud-security-review

A practical AWS multi-account security review framework built from real-world assessment experience. Covers all major security domains with a Python automation layer for evidence collection and a structured remediation output format.

Used to assess 3 production AWS accounts across every security domain, producing prioritized remediation roadmaps for engineering leadership.

---

## Security Domains Covered

| Domain | Key Controls Assessed |
|---|---|
| **IAM** | Least privilege, unused credentials, root account usage, access key rotation, MFA enforcement |
| **GuardDuty** | Enablement, findings review, threat intelligence feeds, suppression rules |
| **CloudTrail** | Multi-region logging, log file validation, S3 bucket protection, CloudWatch integration |
| **S3** | Public access block, bucket policies, ACLs, encryption, versioning, replication |
| **VPC** | Security groups, NACLs, flow logs, peering configurations, internet gateway exposure |
| **KMS** | Key rotation, key policies, unused keys, cross-account access |
| **Security Hub** | Enablement, standards compliance (CIS, AWS Foundational), finding aggregation |
| **Config** | Rules enablement, conformance packs, remediation actions |
| **EC2** | IMDSv2 enforcement, public AMIs, unattached EBS volumes, snapshot sharing |
| **Lambda** | Function policies, execution roles, environment variable secrets |
| **RDS** | Public accessibility, encryption at rest, automated backups, IAM authentication |

---

## Repository Structure

```
cloud-security-review/
├── checklist/
│   ├── aws-security-checklist.md      # Full domain-by-domain checklist
│   └── rapid-assessment-checklist.md  # Condensed version for quick reviews
├── scripts/
│   ├── collect_evidence.py            # Automated evidence collection via boto3
│   ├── iam_analyzer.py                # IAM-specific deep analysis
│   ├── s3_exposure_check.py           # S3 public exposure scanner
│   └── guardduty_findings.py          # GuardDuty findings extractor
├── templates/
│   ├── remediation-roadmap.md         # Remediation output template
│   └── executive-summary.md           # Executive summary template
├── scoring/
│   └── risk-scoring.md                # EPSS + asset criticality scoring methodology
└── README.md
```

---

## Quick Start

### Prerequisites

```bash
pip install boto3 pandas rich
aws configure  # or use assumed role / SSO
```

### Run Evidence Collection

```bash
python scripts/collect_evidence.py \
  --profile YOUR_AWS_PROFILE \
  --regions us-east-1 us-west-2 \
  --output evidence/
```

### Run IAM Analysis

```bash
python scripts/iam_analyzer.py \
  --profile YOUR_AWS_PROFILE \
  --output iam-findings.json
```

---

## Remediation Prioritization

Findings are prioritized using a combination of:

1. **EPSS score** — probability of exploitation in the next 30 days
2. **Asset criticality** — production vs. staging vs. sandbox weighting
3. **Blast radius** — how many resources/accounts are affected
4. **Ease of remediation** — quick wins vs. complex changes

See [scoring/risk-scoring.md](scoring/risk-scoring.md) for the full methodology.

---

## Output Format

Each assessment produces:

- **Findings JSON** — machine-readable findings with severity, resource, and remediation guidance
- **Remediation Roadmap** — prioritized list organized by effort and impact
- **Executive Summary** — non-technical summary suitable for leadership reporting

---

## Multi-Account Usage

For organizations with AWS Organizations, run the collector with cross-account role assumption:

```bash
python scripts/collect_evidence.py \
  --role-arn arn:aws:iam::ACCOUNT_ID:role/SecurityAuditRole \
  --external-id YOUR_EXTERNAL_ID \
  --regions us-east-1 \
  --output evidence/account-123/
```

Repeat for each account and aggregate findings for org-wide visibility.
