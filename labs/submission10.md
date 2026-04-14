# Lab 10 — Vulnerability Management & Response

## Task 1 — DefectDojo Setup

DefectDojo was successfully deployed locally using Docker Compose.

- Application is available at: http://localhost:8080
- Login performed using the default admin account
- The following structure was created:
  - Product Type: Engineering
  - Product: Juice Shop
  - Engagement: Labs Security Testing

The platform is operational and ready for vulnerability management.

---

## Task 2 — Import Results

Security findings from previous labs were imported using the provided automation script.

| Tool     | Findings |
|----------|--------:|
| ZAP      | 12      |
| Semgrep  | 25      |
| Trivy    | 191     |
| Nuclei   | 9       |
| Grype    | 167     |

All findings were successfully imported and are visible within the DefectDojo engagement.

---

## Task 3 — Metrics & Reporting

### Severity Distribution (Active Findings)

- Critical: 55  
- High: 475  
- Medium: 316  
- Low: 92  
- Informational: 40  

Total findings: 978

---

### Open vs Closed Findings

- All findings are currently active (open)
- No findings have been mitigated or closed
- This represents the initial state after bulk import

---

### Findings by Tool

- The majority of findings originate from dependency scanning tools (Trivy and Grype)
- Semgrep contributes static code analysis findings (SAST)
- ZAP and Nuclei provide dynamic analysis (DAST) results

---

### SLA Outlook

- Critical findings should be resolved within 7 days
- High severity findings within 14 days
- Medium severity findings within 30 days

Given the large number of open findings, there is a significant risk of SLA breaches without prioritization and remediation efforts.

---

### Common Vulnerability Types

The most frequent vulnerability categories include:

- Injection vulnerabilities (e.g., SQL Injection)
- Cross-Site Scripting (XSS)
- Vulnerable dependencies
- Information disclosure issues

---

### Key Insights

- A total of 978 findings were imported across multiple tools
- High severity issues dominate the dataset (~48%)
- 55 critical vulnerabilities require immediate attention
- Dependency scanners (Trivy and Grype) contribute the largest volume of findings
- The system is currently in an early stage with no remediation applied

---

## Artifacts

- `labs/lab10/report/metrics-snapshot.md`
- `labs/lab10/report/findings.csv`
- `labs/lab10/report/dojo-report.html` (or PDF)
