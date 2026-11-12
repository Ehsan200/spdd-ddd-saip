# QA-<ATTR>-<nnn>: <short name>
Attribute: <availability | deployability | energy efficiency | integrability | modifiability | performance | safety | security | testability | usability>
Utility-tree rating: (business importance: H/M/L, technical risk: H/M/L)
Status: draft | approved · Owner: <…> · SAiP 4e chapter: <n>

## Six-part scenario
| Part | Value |
|---|---|
| Source of stimulus | <who/what generates the stimulus> |
| Stimulus | <the event or condition> |
| Artifact | <the part of the system stimulated> |
| Environment | <normal / peak load / degraded / startup / …> |
| Response | <what the system must do> |
| **Response measure** | <the testable number — latency, MTTR, availability %, time-to-change, defect escape rate…> |

## One-line form
<“A <source> <stimulus> on <artifact> during <environment>; the system <response> within <measure>.”>

## Applied tactics
| Tactic (category — name) | Where applied | ADR |
|---|---|---|

## Verification
<How the response measure is checked: load test, chaos drill, change-task timing, audit…>
