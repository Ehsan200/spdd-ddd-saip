# Utility Tree — <system name>
Last re-ranked: <yyyy-mm-dd> · Re-rank when business goals shift or after each mini-ATAM.

Rating = (Business importance, Technical risk), each H/M/L.
(H,H) leaves: deep analysis + explicit tactics + ADRs. (L,L): sensible defaults, no ceremony.

## Utility
- **Performance**
  - <refinement, e.g. API latency>
    - (H,M) QA-PERF-001: <one-line scenario> → docs/quality/scenarios/QA-PERF-001.md
- **Modifiability**
  - <refinement, e.g. add new pricing plan>
    - (H,H) QA-MOD-001: <one-line> → <link>
- **Availability**
  - <refinement>
    - (M,H) QA-AVAIL-001: <one-line> → <link>
- **Security**
  - <refinement>
    - (H,L) QA-SEC-001: <one-line> → <link>
- **Testability / Deployability / Integrability / Usability / Safety / Energy efficiency**
  - <add branches only for attributes that matter to this system>

## Top scenarios (current focus — drives mini-ATAM walks)
1. <QA-…-… (H,H)>
2. <QA-…-… (H,M)>
3. <…>
