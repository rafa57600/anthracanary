# Failure Taxonomy for Engineering Pre-Mortems

A comprehensive classification of failure modes organized by coverage area.
Use this taxonomy to systematically identify risks during a pre-mortem analysis.

## 1. Architecture Failures

| ID | Failure Mode | Description | Cascade Risk |
|----|-------------|-------------|-------------|
| ARCH-01 | Single Point of Failure | A single component whose loss takes down the system | High |
| ARCH-02 | Leaky Abstractions | Internal implementation details exposed, creating tight coupling | Medium |
| ARCH-03 | No Graceful Degradation | System crashes entirely instead of degrading features | High |
| ARCH-04 | Synchronous Coupling | Chain of synchronous calls with no timeouts or circuit breakers | High |
| ARCH-05 | Eventual Consistency Ignored | System assumes strong consistency where only eventual exists | Medium |
| ARCH-06 | No Bulkhead Isolation | One failing tenant/feature takes down shared resources | High |
| ARCH-07 | Improper State Management | State stored in process memory with no externalization | High |

## 2. Runtime Behavior Failures

| ID | Failure Mode | Description | Cascade Risk |
|----|-------------|-------------|-------------|
| RUNT-01 | Memory Leaks | Unbounded growth in heap, cache, or connection pools | Medium |
| RUNT-02 | Connection Pool Exhaustion | Pool size configured for peak, not for failure recovery | High |
| RUNT-03 | Thread Starvation | Blocking calls on constrained thread pools | High |
| RUNT-04 | Unbounded Retries | Infinite retry loops with no exponential backoff | High |
| RUNT-05 | Slow Locks | Distributed lock contention under load | Medium |
| RUNT-06 | No Load Shedding | System accepts all requests until OOM or connection refused | High |

## 3. Security Failures

| ID | Failure Mode | Description | Cascade Risk |
|----|-------------|-------------|-------------|
| SEC-01 | Credential Leakage | Secrets in logs, env dumps, error messages | Critical |
| SEC-02 | No Rate Limiting | Public endpoints vulnerable to abuse | High |
| SEC-03 | Improper AuthZ | Missing or incorrect authorization checks | Critical |
| SEC-04 | Injection Vectors | Unsanitized input in queries, shells, or eval contexts | Critical |
| SEC-05 | Supply Chain | Compromised dependency with elevated privileges | High |
| SEC-06 | Side-Channel via Timing | Timing differences leak internal state | Medium |

## 4. Scalability Failures

| ID | Failure Mode | Description | Cascade Risk |
|----|-------------|-------------|-------------|
| SCALE-01 | No Horizontal Scaling | Architecture assumes single instance | High |
| SCALE-02 | Data Skew | Hot partitions or shards under load | Medium |
| SCALE-03 | Thundering Herd | All instances restart and hit DB simultaneously | High |
| SCALE-04 | No Connection Pooling per Tenant | Each tenant creates fresh connections | High |
| SCALE-05 | N+1 Query Pattern | ORM generates exponential queries on list endpoints | Medium |
| SCALE-06 | Unbounded Queue Growth | Work queue grows faster than consumers drain | High |

## 5. AI / Agent Behavior Failures

| ID | Failure Mode | Description | Cascade Risk |
|----|-------------|-------------|-------------|
| AI-01 | Hallucination Propagation | Model outputs fed back as input without validation | Critical |
| AI-02 | Prompt Injection | Untrusted input contaminates system prompt context | Critical |
| AI-03 | No Output Guardrails | No schema or semantic validation on model output | High |
| AI-04 | Runaway Agent Loops | Agent retries indefinitely, burning tokens and API calls | High |
| AI-05 | Context Window Overflow | Prompt grows unbounded until truncation or OOM | Medium |
| AI-06 | Drift Over Time | Model behavior changes after deployment without detection | Medium |
| AI-07 | No Human-in-the-Loop | Autonomous actions with no approval gate for destructive ops | Critical |

## 6. Integration Failures

| ID | Failure Mode | Description | Cascade Risk |
|----|-------------|-------------|-------------|
| INT-01 | No Circuit Breaker | Cascading failure when downstream is degraded | High |
| INT-02 | API Contract Drift | Downstream API changes without notice | Medium |
| INT-03 | No Fallback Mode | Integration failure blocks the entire flow | High |
| INT-04 | Silent Data Corruption | Data transformation mismatch silently corrupts data | High |
| INT-05 | Webhook Flood | Unthrottled webhook delivery overwhelms the receiver | Medium |
| INT-06 | Stale Cache | Integration uses cached data past TTL without refresh | Medium |

## 7. DevOps / Infrastructure Failures

| ID | Failure Mode | Description | Cascade Risk |
|----|-------------|-------------|-------------|
| OPS-01 | No Rollback Plan | Deployment cannot be reverted cleanly | High |
| OPS-02 | Config Drift | Environments differ in non-obvious ways | Medium |
| OPS-03 | Insufficient Observability | No metrics, traces, or logs to diagnose | High |
| OPS-04 | No Disaster Recovery | No documented RTO/RPO or tested DR plan | Critical |
| OPS-05 | Certificate Expiry | TLS cert expires silently, breaking all TLS connections | High |
| OPS-06 | Resource Exhaustion | Disk, inode, or fd exhaustion from unbounded growth | Medium |

## 8. Human / Process Failures

| ID | Failure Mode | Description | Cascade Risk |
|----|-------------|-------------|-------------|
| PROC-01 | Knowledge Silos | Only one person knows critical subsystem | Medium |
| PROC-02 | No Incident Response | No documented runbook or on-call rotation | High |
| PROC-03 | Manual Deployment | Human-in-the-loop deployment is error-prone | High |
| PROC-04 | Compliance Blindspot | Regulatory requirement missed in design | Medium |
| PROC-05 | No Capacity Planning | Team discovers scaling limits during incidents | Medium |
| PROC-06 | Burnout-Driven Errors | Ops fatigue causes operational mistakes | Medium |
