# Lab 8 — Software Supply Chain Security

## Task 1 — Signing & Verification

- Pulled and pushed image to local registry
- Used digest reference instead of tag
- Signed image with Cosign
- Verified signature successfully

Tampering demonstration:
- Replaced image with another (busybox)
- New digest failed verification
- Original digest still verified

Conclusion:
Signing protects against tag tampering because signatures are bound to image digest.

---

## Task 2 — Attestations

- Generated SBOM (CycloneDX)
- Attached using cosign attest
- Verified with cosign verify-attestation

SBOM attestation contains:
- list of dependencies
- metadata about image contents

Provenance attestation provides:
- build metadata
- traceability of image origin

Difference from signatures:
- signature → proves integrity
- attestation → provides metadata about artifact

---

## Task 3 — Artifact Signing

- Created tar.gz artifact
- Signed using cosign sign-blob (bundle format)
- Verified successfully

Blob signing use cases:
- release binaries
- configuration files
- backups

Difference:
- container signing uses registry + digest
- blob signing uses local files and signatures