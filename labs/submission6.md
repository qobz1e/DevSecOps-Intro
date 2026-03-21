# Lab 6 — Infrastructure-as-Code Security Analysis

## Overview

This lab focuses on identifying security vulnerabilities in Infrastructure-as-Code (IaC) using multiple tools across Terraform, Pulumi, and Ansible configurations. The goal is to compare tool effectiveness and provide security recommendations.

---

# Task 1 — Terraform & Pulumi Security Scanning

## Terraform Tool Comparison

| Tool       | Findings |
|------------|---------|
| tfsec      | 53      |
| Checkov    | 78      |
| Terrascan  | 22      |

### Analysis

- **Checkov** detected the highest number of issues due to its large policy library.
- **tfsec** provided focused Terraform-specific findings with lower noise.
- **Terrascan** identified fewer issues but focused more on compliance and policy enforcement.

## Tool Strengths

- **tfsec**
  - Fast and simple
  - Low false positives
  - Best for CI/CD pipelines

- **Checkov**
  - Extensive rule set
  - Multi-framework support
  - Detects IAM, encryption, and secrets issues

- **Terrascan**
  - Policy-based (OPA)
  - Good for compliance (PCI, HIPAA)
  - Clear policy violations

---

## Pulumi Security Analysis (KICS)

| Severity | Count |
|----------|------|
| Critical | 1    |
| High     | 2    |
| Medium   | 1    |
| Low      | 0    |
| Info     | 2    |
| **Total**| 6    |

### Key Findings

1. **Publicly accessible RDS instance (CRITICAL)**
   - `publiclyAccessible: true`
   - Risk: external database exposure

2. **Unencrypted DynamoDB table (HIGH)**
   - Missing encryption configuration

3. **Hardcoded database password (HIGH)**
   - Secret stored in configuration

4. **EC2 monitoring disabled (MEDIUM)**
   - Reduced visibility

5. **No point-in-time recovery (INFO)**
   - Data recovery risk

---

## Terraform vs Pulumi

| Aspect            | Terraform              | Pulumi                  |
|------------------|----------------------|--------------------------|
| Format           | Declarative (HCL)     | YAML / Code              |
| Findings Count   | High (22–78)          | Lower (6)                |
| Complexity       | Simpler               | More flexible            |
| Risk             | Misconfiguration      | Logic + config issues    |

**Conclusion:**
- Terraform has more configuration-related risks
- Pulumi introduces risks from both config and logic

---

# Task 2 — Ansible Security Analysis (KICS)

## Findings Summary

| Severity | Count |
|----------|------|
| High     | 9    |
| Medium   | 0    |
| Low      | 1    |
| **Total**| 10   |

## Key Security Issues

1. **Hardcoded credentials**
   - Found in playbooks and inventory
   - High risk of compromise

2. **Passwords in URLs**
   - Exposes sensitive data

3. **Root user usage**
   - Violates least privilege principle

4. **Unpinned package versions**
   - Risk of unstable or malicious updates

---

## Best Practice Violations

1. **Secrets stored in plaintext**
   - Should use Ansible Vault

2. **Use of root user**
   - Should use limited privilege accounts

3. **No `no_log` for sensitive data**
   - Logs may expose secrets

---

## Remediation Steps

- Use **Ansible Vault** for secrets
- Add `no_log: true` to sensitive tasks
- Avoid root user, use `become` properly
- Pin package versions
- Remove credentials from inventory files

---

# Task 3 — Comparative Tool Analysis

## Tool Comparison Matrix

| Criterion              | tfsec | Checkov | Terrascan | KICS |
|-----------------------|------|---------|-----------|------|
| Total Findings        | 53   | 78      | 22        | 16   |
| Scan Speed            | Fast | Medium  | Medium    | Medium |
| False Positives       | Low  | Medium  | Low       | Medium |
| Report Quality        | ⭐⭐⭐  | ⭐⭐⭐⭐   | ⭐⭐⭐      | ⭐⭐⭐⭐ |
| Ease of Use           | ⭐⭐⭐⭐ | ⭐⭐⭐    | ⭐⭐⭐      | ⭐⭐⭐ |
| Platform Support      | Terraform | Multi | Multi | Multi |
| CI/CD Integration     | Easy | Easy    | Medium    | Medium |

---

## Vulnerability Category Analysis

| Category              | Best Tool |
|----------------------|----------|
| Encryption Issues    | Checkov  |
| Network Security     | tfsec    |
| Secrets Management   | KICS     |
| IAM/Permissions      | Checkov  |
| Compliance           | Terrascan|

---

## Top 5 Critical Findings

1. Public RDS database (Pulumi)
2. Hardcoded secrets (Ansible & Pulumi)
3. Unencrypted storage (Terraform & Pulumi)
4. Open security groups (Terraform)
5. Root access in Ansible inventory

---

## Tool Selection Guide

- **tfsec**
  - Use in CI for fast Terraform checks

- **Checkov**
  - Use for deep analysis and multiple frameworks

- **Terrascan**
  - Use for compliance and policy enforcement

- **KICS**
  - Best for Pulumi and Ansible scanning

---

## CI/CD Integration Strategy

1. **Pre-commit**
   - tfsec (fast checks)

2. **CI pipeline**
   - Checkov + KICS

3. **Security stage**
   - Terrascan for compliance

4. **Policy enforcement**
   - Block deployment on critical findings

---

## Lessons Learned

- No single tool provides full coverage
- Combining tools improves detection rate
- Secrets management is the most common issue
- IaC introduces critical risks if not validated

---

## Conclusion

Using multiple IaC scanning tools significantly improves security coverage. Each tool specializes in different areas, and combining them provides a comprehensive security assessment. Integrating these tools into CI/CD pipelines ensures early detection and remediation of vulnerabilities.