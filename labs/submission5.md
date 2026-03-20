# Lab 5 — Security Analysis: SAST & DAST of OWASP Juice Shop

![difficulty](https://img.shields.io/badge/difficulty-intermediate-orange)
![topic](https://img.shields.io/badge/topic-SAST%20%26%20DAST-blue)
![points](https://img.shields.io/badge/points-10-orange)

## SAST Analysis — Semgrep

**Findings:**
- Total SAST findings: 0
- Types detected: None (no code-level vulnerabilities flagged)
- Coverage: All cloned source code scanned

**Critical Findings:**
- None detected

---

## DAST Analysis — Multi-Tool

**ZAP (Unauthenticated & Authenticated)**
- WARN-NEW: 9
- Notes: Authenticated scan revealed headers missing, CSP not set, and JavaScript/CORS misconfigurations
- Example endpoints: `http://localhost:3001/ftp/`, `/polyfills.js`, `/styles.css`

**Nuclei**
- Findings: 0 template matches
- Notes: No known CVEs detected

**Nikto**
- Findings: 82 server issues
- Example findings:
  - Potentially interesting backup/cert files in `/ftp/`, `/archive.*`, `/dump.*`
  - Missing security headers: `X-XSS-Protection`, `feature-policy`
  - Interesting entries in `robots.txt`

**SQLmap**
- Findings: 1 SQL injection vulnerability
- Example: `http://localhost:3001/rest/products/search?q=*`

---

## SAST/DAST Correlation

**Summary Table:**

| Tool    | Findings | Severity/Notes                                 |
|---------|---------:|-----------------------------------------------|
| Semgrep | 0        | No code-level vulnerabilities found           |
| ZAP     | 9        | Missing headers, CSP not set, JS/CORS issues |
| Nuclei  | 0        | No template matches detected                  |
| Nikto   | 82       | Server misconfigurations and info leaks      |
| SQLmap  | 1        | SQL injection in products search endpoint    |

**Key Insights:**
- SAST (Semgrep) finds code-level issues; DAST finds runtime vulnerabilities
- Nikto excels at server misconfigurations
- SQLmap finds deep SQL injection issues that code scan may miss
- ZAP scans authenticated endpoints and highlights missing headers / CSP issues
- Using both SAST & DAST gives comprehensive coverage

**Recommendations:**
- Integrate Semgrep in CI/CD for early detection of code-level vulnerabilities
- Run ZAP with authenticated scans in staging before production
- Use Nuclei for quick CVE detection
- Periodically run Nikto to check server configuration
- Use SQLmap for targeted SQL injection testing

---

**Conclusion:**
- Total findings: 0 (SAST) + 92 (DAST combined)
- No critical code-level vulnerabilities detected, but runtime misconfigurations exist
- Combining static and dynamic analysis ensures comprehensive security coverage