# Lab 9 — Submission

## Task 1 — Falco Runtime Detection

### Baseline Alerts
Falco detected multiple runtime security events inside `lab9-helper` container:

- Shell execution inside container (Run shell untrusted)
- File writes under `/usr/local/bin` (drift detection)
- Additional syscall-based alerts from event-generator (fileless execution, sensitive file access)

### Custom Falco Rule

Rule: Write Binary Under UsrLocalBin

Purpose:
Detect any file creation or modification under `/usr/local/bin` inside containers.

Why it matters:
- This directory is commonly used for placing executables
- Attackers may inject malicious binaries or modify PATH-executed tools

Trigger condition:
- Write operations (open/openat/creat)
- Path starts with `/usr/local/bin`
- Container context (not host)

Observed output:
- Rule triggered successfully when writing test files inside container

---

## Task 2 — Conftest Policy-as-Code

### Unhardened Manifest Violations

The following security issues were detected:

- Missing CPU/memory limits → risk of resource exhaustion (DoS)
- Missing resource requests → unpredictable scheduling behavior
- Privilege escalation allowed → container can escalate privileges
- Running as root → increases blast radius of compromise
- No readOnlyRootFilesystem → attacker can modify container filesystem
- Missing runAsNonRoot → weak isolation model
- Using :latest tag → non-deterministic deployments

Security impact:
These issues collectively violate Kubernetes hardening best practices and increase attack surface significantly.

---

### Hardened Manifest

All policies passed successfully (30/30 tests):

Improvements applied:
- runAsNonRoot enabled
- allowPrivilegeEscalation disabled
- readOnlyRootFilesystem enabled
- resource limits and requests defined
- stable image tag used instead of :latest

---

### Docker Compose Analysis

Compose file passed all policy checks (15/15 tests):

- No privileged containers
- No missing resource constraints
- Security baseline satisfied

---

## Conclusion

- Falco successfully detected runtime anomalies and custom rule violations
- Conftest enforced Kubernetes and Docker Compose security policies
- Hardened manifests comply with production security standards