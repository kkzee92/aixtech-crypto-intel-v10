# Security architecture (v0.10 enhanced)

Version 0.10 expands StochRSI filters, Ichimoku-style cloud proxies, dynamic sizing, broader secret patterns (including seed proxies, more base64), enhanced anomaly (skewness), multi-hash, and refined zero-trust. Research design only; not certified, not legal advice.

## Trust boundaries

```mermaid
flowchart LR
    F[\"Synthetic fixture or injected public ticker\"] --> AT[\"Source attestation + multi-hash + statistical anomaly + correlation + StochRSI\"]
    AT --> S[\"Strategy rules + multi-scale regime + VWAP/Bollinger/ADX/cloud + adaptive\"]
    S --> R[\"Risk gate: class caps, kill switch, drawdown, adaptive size/confidence + anomaly/corr\"]
    R --> P[\"Paper fill only\"]
    P --> A[\"Hash-chained audit with enhanced sanitization and integrity tags\"]
    K[\"Trade or withdrawal keys\"] -.-> X[\"Refused. Never loaded\"]
    RBAC[\"Researcher, auditor, operator\"] --> R
```

No order router. Paper-only enforced. Zero-trust: every input attested, scored, and sanitized.

## Roles

Unchanged: Researcher / Auditor / Operator. No trader role. Broker is separate.

## Data classes and handling

Expanded redaction for additional secret formats including seed phrases proxies. Wallet, personal data, trade keys refused or redacted. Source must be SYNTHETIC or PUBLIC_READ with checksums.

## Feed integrity and anomaly

Enhanced anomaly_score includes return z-score, volume z-score, consecutive anomalies, skewness proxy, multi-bar consistency. High scores reduce size or block in risk gate. Correlation check for relative strategies. StochRSI gates prevent extremes.

## Threats and controls

| Threat | Control |
|---|---|
| Secret committed | gitleaks + expanded redaction (bearer, conn strings, seed proxies, more key formats) |
| Trade key pasted | refuse_trade_secret |
| Fixture injection | SYNTHETIC + multi-hash + attestation + anomaly |
| Stale/jumped/anomalous data | multi-stat anomaly + consistency + correlation + StochRSI gates |
| Oversized risk | class caps + adaptive floors + anomaly size reduction + dynamic vol sizing |
| Runaway loss | drawdown + kill switch |
| Audit tamper | SHA-256 chain + verify |
| Live mistake | mode guard, no client, RBAC, paper-only |
| Personal data / injection | tripwire + sanitization + length limits |
| Privilege creep | no order role |

## Incident handling

1. Engage kill switch.
2. Freeze audit; record attestation digests, high anomaly/correlation scores.
3. Rotate secrets externally; purge history.
4. No public issues containing secrets or personal data.
5. Human review before clearing kill switch.
6. Update patterns post-incident.

## Operating rules

- Read-only credentials only if ever added; never logged.
- All logs sanitized.
- Default paper/research. Broker separate and human-approved.
- Zero-trust assumptions.
- Strategies and gates versioned with tests and human review.
- Adaptive risk reduces exposure under anomaly or correlation mismatch.

Prioritizes research integrity and prevention of accidental live execution or data leakage.
