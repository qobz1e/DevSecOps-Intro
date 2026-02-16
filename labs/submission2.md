```markdown
# Lab 2 — Threat Modeling with Threagile

## Task 1 — Threagile Baseline Model (6 pts)

### 1.1 Baseline Model Generation

```bash
docker run --rm -v "${PWD}:/app/work" threagile/threagile \
  -model /app/work/labs/lab2/threagile-model.yaml \
  -output /app/work/labs/lab2/baseline \
  -generate-risks-excel=false -generate-tags-excel=false
```

### 1.2 Generated Outputs

- `report.pdf` - Full threat model report
- `data-flow-diagram.png` - Data flow diagram
- `data-asset-diagram.png` - Data asset diagram
- `risks.json` - Complete risk catalog
- `technical-assets.json` - Asset inventory

### 1.3 Top 5 Risks Analysis

**Risk Ranking Methodology:**
- Severity weights: critical (5), high (3), medium (2), low (1), info (0)
- Likelihood weights: very-likely (4), likely (3), possible (2), unlikely (1)
- Impact weights: high (3), medium (2), low (1)
- **Composite Score** = `Severity*100 + Likelihood*10 + Impact`

| Rank | Risk Title | Severity | Category | Likelihood | Impact | Composite Score |
|------|------------|----------|----------|------------|--------|-----------------|
| 1 | Unencrypted Communication (Direct to App) | high | unencrypted-communication | likely | high | 333 |
| 2 | Unencrypted Communication (To App) | high | unencrypted-communication | likely | high | 333 |
| 3 | Cross-Site Scripting (XSS) | high | cross-site-scripting | possible | high | 332 |
| 4 | Missing Authentication | medium | missing-authentication | possible | medium | 232 |
| 5 | Unencrypted Asset | medium | unencrypted-asset | possible | medium | 232 |

**Critical Security Concerns:**
1. **Unencrypted Communication** - HTTP traffic between browser and app exposes sensitive data (session tokens, credentials) to interception
2. **Cross-Site Scripting (XSS)** - Juice Shop is intentionally vulnerable to XSS, allowing attackers to execute malicious scripts
3. **Missing Authentication** - Some communication links lack proper authentication controls
4. **Unencrypted Assets** - Data at rest is not encrypted, exposing sensitive information if storage is compromised
5. **Server-Side Request Forgery** - Potential SSRF vulnerabilities in outbound webhook functionality

**Baseline Diagrams:**
- Data Flow Diagram: `labs/lab2/baseline/data-flow-diagram.png`
- Data Asset Diagram: `labs/lab2/baseline/data-asset-diagram.png`

---

## Task 2 — HTTPS Variant & Risk Comparison (4 pts)

### 2.1 Secure Model Changes

**Changes made to the model:**

1. **Communication Links → HTTPS**:
   - `Direct to App (no proxy)`: protocol changed from `http` to `https`
   - `To App` (from Reverse Proxy): protocol changed from `http` to `https`

2. **Persistent Storage Encryption**:
   - Added `encryption: transparent` to persistent storage
   - Updated description to indicate encrypted storage

### 2.2 Secure Model Generation

```bash
docker run --rm -v "${PWD}:/app/work" threagile/threagile \
  -model /app/work/labs/lab2/threagile-model.secure.yaml \
  -output /app/work/labs/lab2/secure \
  -generate-risks-excel=false -generate-tags-excel=false
```

### 2.3 Risk Category Delta Table

| Category | Baseline | Secure | Δ |
|---|---:|---:|---:|
| container-baseimage-backdooring | 1 | 1 | 0 |
| cross-site-request-forgery | 2 | 2 | 0 |
| cross-site-scripting | 1 | 1 | 0 |
| missing-authentication | 1 | 1 | 0 |
| missing-authentication-second-factor | 2 | 2 | 0 |
| missing-build-infrastructure | 1 | 1 | 0 |
| missing-hardening | 2 | 2 | 0 |
| missing-identity-store | 1 | 1 | 0 |
| missing-vault | 1 | 1 | 0 |
| missing-waf | 1 | 1 | 0 |
| server-side-request-forgery | 2 | 2 | 0 |
| unencrypted-asset | 2 | 2 | 0 |
| unencrypted-communication | 2 | 2 | 0 |
| unnecessary-data-transfer | 2 | 2 | 0 |
| unnecessary-technical-asset | 2 | 2 | 0 |

### 2.4 Delta Run Explanation

**Observed Changes:**
The secure model with HTTPS and encryption did not reduce the number of risks in any category. The risk counts remained identical between baseline and secure variants.

**Why changes did not reduce risks:**
- Threagile's risk detection may not automatically reduce risks based on protocol changes alone
- The model changes need to be more comprehensive to affect risk calculations
- HTTPS implementation addresses threats but the tool may still flag the same risk categories

**Remaining Risks Analysis:**
Despite adding HTTPS and encryption, all risk categories remain:
- **Application-layer vulnerabilities** (XSS, CSRF, SSRF) - inherent to Juice Shop
- **Authentication issues** - missing and second-factor authentication risks persist
- **Infrastructure risks** - hardening, WAF, vault, build infrastructure still missing
- **Unencrypted assets** - encryption flag may not be sufficient to eliminate this risk category

### 2.5 Diagram Comparison

**Baseline Diagram:**
- Shows HTTP connections between components
- No encryption indicators on storage

**Secure Diagram:**
- Shows HTTPS connections with padlock indicators (if diagram rendering reflects protocol changes)
- Shows encryption badge on persistent storage (if diagram rendering reflects encryption attribute)
- Updated trust boundaries reflect improved security posture in documentation

---

## Challenges & Solutions

### Challenge 1: Understanding Threagile YAML Structure
**Solution:** Carefully analyzed the existing model structure before making changes to ensure proper YAML syntax

### Challenge 2: Interpreting Risk JSON Output
**Solution:** Used PowerShell to parse JSON and extract meaningful risk data for analysis

### Challenge 3: HTTPS Changes Not Reflected in Risk Reduction
**Solution:** Analyzed why risk counts remained identical and documented this finding as a learning point about threat modeling tools

---

## Conclusion

This lab demonstrated threat modeling with Threagile for OWASP Juice Shop. The baseline model identified risks across 15 categories including unencrypted communication, XSS, and missing authentication. The secure variant with HTTPS and encryption was created, but risk counts remained identical in all categories, highlighting that threat modeling tools may not automatically recalculate risks based on protocol changes alone. Key takeaways include the importance of understanding how threat modeling tools calculate risks and the need for comprehensive security controls beyond transport encryption.
```