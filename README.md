# Post-Quantum Readiness Scorecard

**Scan your repository for quantum-vulnerable cryptography, get an A–F readiness grade + a CycloneDX 1.6 CBOM
(Cryptographic Bill of Materials), and optionally fail the build on broken crypto — in one CI step, no install.**

Regulators are starting to require a cryptographic inventory (CNSA 2.0, DORA, NIS2, EU CRA). This Action produces
that inventory automatically on every push. Free and open-source (MIT). Zero dependencies — the scanner is vendored.

> Prefer to try it in your browser first (nothing uploaded)? → **https://throndar.ai/cbom**

## Usage

```yaml
# .github/workflows/pqc-readiness.yml
name: PQC readiness
on: [push, pull_request]
jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: brandonjsellam-Releone/pq-readiness-scorecard@v1
        with:
          path: .
          fail-on: broken-classical      # fail the build on classically-broken crypto
          # min-grade: B                  # optional: fail below this grade
```

## Inputs

| Input | Default | Description |
|---|---|---|
| `path` | `.` | Directory to scan |
| `fail-on` | `broken-classical` | Comma-separated risk classes that fail the build (`broken-classical,quantum-broken,quantum-weakened`) |
| `min-grade` | `` (off) | Fail below this grade (A–F) |

## Outputs

| Output | Description |
|---|---|
| `grade` | Post-Quantum Readiness grade (A–F) |
| `score` | Score 0–100 |
| `sarif-file` | SARIF 2.1.0 report — upload with `github/codeql-action/upload-sarif` to see findings in the **Security** tab |
| `cbom-file` | `cbom.cdx.json` — CycloneDX 1.6 CBOM |

## What it detects

RSA / ECDSA / ECDH / DH / EC curves (quantum-broken by Shor) · AES-128/192, SHA-256/384 (quantum-weakened by
Grover) · MD5 / SHA-1 / RC4 / 3DES / Blowfish / deprecated TLS / NTLM / WEP (classically broken) · X25519 / Ed25519
(flagged as valid *hybrid* legs) · ML-KEM / ML-DSA / SLH-DSA / Falcon / XMSS / AES-256 / SHA-512 / ChaCha20
(quantum-resistant). It also flags **broken PQ candidates** — SIKE/SIDH (Castryck–Decru 2022) and GeMSS — so a
project that *thinks* it migrated isn't left with a false sense of safety. Reads declared crypto libraries, numeric
OIDs (certs/ASN.1), and base64/PEM key/cert blobs too.

## Honest posture

Lexical scan — findings are **leads to verify, not a complete inventory** and **not a certification**. Algorithm
names denote the public standards they're based on, not a CMVP/FIPS-140 validation. It won't fake a clean bill of
health: a scan that examines zero files refuses to grade rather than reporting "A". Falcon is FN-DSA for the
forthcoming FIPS 206 (in development), not yet standardized.

## Need a signed, auditor-ready report?

An **Evidence Pack** turns the scan into a cryptographically signed, independently-verifiable deliverable (exec
summary + grade + findings + CBOM + migration plan) that your auditors can verify offline. → **https://throndar.ai/evidence**

## License

MIT © TRELYAN Inc. The scanner is open-source; read it, re-run it, verify every finding yourself.
