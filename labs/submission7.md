# Lab 7 — Container Security: Image Scanning & Deployment Hardening

## 🔹 Task 1 — Image Vulnerability & Configuration Analysis

### Docker Scout & Snyk Vulnerability Analysis

The container image `bkimminich/juice-shop:v19.0.0` was analyzed using Docker Scout and Snyk.

### Top 5 Critical/High Vulnerabilities

1. **CVE-2025-69421 (OpenSSL / libssl3)**  
   - Severity: High  
   - Impact: Vulnerabilities in cryptographic operations may allow data compromise  
   - Fix: Upgrade to `3.0.18`

2. **Node.js Race Condition**  
   - Severity: Critical  
   - Impact: May lead to unpredictable execution and security bypass  
   - Fix: Upgrade to `22.22.0`

3. **Node.js Symlink Vulnerability**  
   - Severity: High  
   - Impact: Unauthorized file access via symbolic link attacks  

4. **JWT Authentication Bypass (jsonwebtoken)**  
   - Severity: High  
   - Impact: Attackers can forge authentication tokens  

5. **Remote Code Execution (vm2)**  
   - Severity: Critical  
   - Impact: Arbitrary code execution inside container  
   - Note: Some issues have no immediate patch  

---

### Vulnerability Summary

- OS-level vulnerabilities: 6  
- Application-level vulnerabilities: 47  
- Multiple Critical and High severity issues detected  
- Many outdated dependencies (lodash, minimatch, qs, etc.)

---

### Dockle Configuration Findings

#### FATAL Issues
- Container runs as root  
- No non-root user specified  

#### WARN Issues
- No HEALTHCHECK instruction  
- Missing security configurations  
- Potential exposure of sensitive data  

---

### Security Risks

- Running as root increases attack surface  
- Missing health checks reduce reliability  
- Vulnerable dependencies expose system to exploits  

---

### Security Posture Assessment

The container has a **weak security posture** due to:

- High number of vulnerabilities  
- Outdated dependencies  
- Lack of runtime restrictions  
- Running with root privileges  

---

### Recommendations

- Upgrade vulnerable dependencies  
- Use non-root user (`USER node`)  
- Add HEALTHCHECK instruction  
- Apply runtime restrictions (cap-drop, seccomp)  
- Use minimal base images  
- Implement secrets management  

---

## 🔹 Task 2 — Docker Host Security Benchmarking

### Execution Result

Docker Bench Security could not be fully executed due to platform limitations.

The environment uses **Docker Desktop on Windows**, which does not provide full access to:

- Linux namespaces  
- `/etc`, `/var/lib` system paths  
- Host-level security configurations  

---

### Expected CIS Benchmark Findings

Based on CIS Docker Benchmark best practices:

- Missing resource limits on containers  
- Containers running with excessive privileges  
- Lack of security options (seccomp, AppArmor)  
- No enforced least-privilege configuration  

---

### Security Impact

Failure to comply with CIS benchmarks may result in:

- Increased attack surface  
- Privilege escalation risks  
- Resource exhaustion vulnerabilities  
- Weak isolation between containers  

---

### Recommendations

- Enforce least privilege principle  
- Enable security profiles (seccomp/AppArmor)  
- Set resource limits for all containers  
- Regularly audit Docker configuration  

---

## 🔹 Task 3 — Deployment Security Configuration Analysis

### Configuration Comparison

| Setting | Default | Hardened | Production |
|--------|--------|----------|------------|
| Capabilities | Default (all) | Dropped ALL | Dropped ALL + NET_BIND_SERVICE |
| Security Options | None | no-new-privileges | no-new-privileges |
| Memory Limit | None (~7.6GB host) | 512MB | 512MB + swap limit |
| CPU Limit | None | 1 CPU | 1 CPU |
| PIDs Limit | None | None | 100 |
| Restart Policy | None | None | on-failure:3 |

---

### Functionality Test

All containers returned:

```

HTTP 200 OK

```

✔️ Security hardening did not affect application functionality.

---

### Resource Usage

| Container | CPU | Memory |
|----------|-----|--------|
| Default | 0.31% | 101 MB / 7.6 GB |
| Hardened | 0.33% | 91 MB / 512 MB |
| Production | 0.77% | 92 MB / 512 MB |

---

### Analysis

- Default container has no limits → risk of resource exhaustion  
- Hardened and Production enforce limits → stable and secure  
- Minimal performance overhead observed  

---

## 🔹 Security Measures Analysis

### a) `--cap-drop=ALL` and `--cap-add=NET_BIND_SERVICE`

Linux capabilities divide root privileges into smaller units.

- Dropping ALL capabilities removes unnecessary privileges  
- Prevents system-level operations and privilege abuse  
- NET_BIND_SERVICE allows binding to low ports  

**Trade-off:**  
Better security vs reduced flexibility  

---

### b) `--security-opt=no-new-privileges`

Prevents processes from gaining additional privileges.

- Blocks privilege escalation attacks  
- Protects against setuid-based exploits  

---

### c) Resource Limits (`--memory`, `--cpus`)

Without limits:
- Containers can consume all host resources  

With limits:
- Prevents DoS attacks  
- Ensures fair resource usage  

---

### d) `--pids-limit=100`

Limits number of processes.

- Prevents fork bomb attacks  
- Protects system stability  

---

### e) `--restart=on-failure:3`

- Automatically restarts container on failure  
- Improves reliability  

**Risk:**  
May hide repeated failures if misconfigured  

---

## 🔹 Critical Thinking

### 1. Development Profile

**Default**

- Easier debugging  
- No restrictions  
- Full access  

---

### 2. Production Profile

**Production**

- Strong security controls  
- Resource limits  
- Least privilege  

---

### 3. Resource Limits Importance

Prevent:
- Denial-of-Service attacks  
- Resource exhaustion  
- System instability  

---

### 4. Attack Comparison

| Attack | Default | Production |
|--------|--------|------------|
| Privilege escalation | Possible | Blocked |
| Resource exhaustion | Possible | Limited |
| Fork bomb | Possible | Prevented |
| Capability abuse | Possible | Restricted |

---

### 5. Additional Hardening

- Run containers as non-root  
- Use read-only filesystem  
- Apply seccomp/AppArmor  
- Limit network access  
- Use image signing  
- Manage secrets securely  

---

## ✅ Conclusion

The analysis shows that:

- The container image contains multiple high-risk vulnerabilities  
- Default container configuration is insecure  
- Security hardening significantly improves protection  
- Production configuration provides strong isolation with minimal performance impact  

Implementing these practices is essential for secure containerized environments in DevSecOps pipelines.