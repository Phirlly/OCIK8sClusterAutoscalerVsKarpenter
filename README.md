# OKE Cluster Autoscaler vs Karpenter Provider for OCI

## Objective
This benchmark compares autoscaling behavior on Oracle Kubernetes Engine (OKE) between:

- OKE Cluster Autoscaler add-on (`ca` target)
- Karpenter Provider for OCI (`kpo` target)

The goal is an apples-to-apples comparison of:
- time to first capacity after pending pods appear
- time for all workload pods to become running
- scale-down completion time
- supporting telemetry and controller behavior

## What Was Executed
Run ID: `ca-vs-kpo-20260420T154619Z`  
Region: `us-ashburn-1`  
Targets: `ca`, `kpo`  
Scenarios: `burst`, `steady`  
Repeats: `3` per target per scenario (12 total runs)  
Scenario window: `2026-04-20T18:20:29.383648Z` to `2026-04-20T23:00:53.166354Z`

## Harness Flow (Notebook Cells)
Notebook: `OCIK8sClusterAutoscalerVsKarpenter/Cluster_Autoscaler_Vs_Karpenter_On_OKE.ipynb`

| Cell | Purpose |
|---|---|
| 1 | Environment checks (no cloud mutation) |
| 2 | SSOT defaults and credential context |
| 3 | Interactive validation of version/shape/image/scenario selections |
| 4 | Generate both Terraform stacks (`tf/ca`, `tf/kpo`) and templates/manifests |
| 5 | Preflight gates (`terraform fmt/validate/plan`, compatibility checks) |
| 6 | Image discovery and pin validation |
| 7 | Provision selected targets via Terraform |
| 8 | Post-provision autoscaler setup and health checks (CA/KPO + IAM) |
| 9 | Deploy identical benchmark workloads at base replicas `0` |
| 10 | Burst scenario execution + scale-down (event ledger + summary) |
| 11 | Steady scenario execution + scale-down (event ledger + summary) |
| 12 | Collect telemetry (OCI Monitoring + K8s evidence + logs + snapshots) |
| 13 | Compute KPIs (per-run + aggregates) |
| 14 | Generate run manifest and comparison report |
| 15 | Cleanup (KPO CR cleanup + Terraform destroy + residual checks) |

## Configuration Used
- Kubernetes version (both clusters): `v1.34.2`
- Shape (all worker pools): `VM.Standard.E5.Flex` (`2 OCPUs`, `16 GiB`)
- Image mode: `pin`
- Pinned image:
  - `Oracle-Linux-8.10-2026.02.28-0-OKE-1.34.2-1392`
- Workload:
  - deployment: `benchmark-load`
  - image: `registry.k8s.io/pause:3.9`
  - requests/limits per pod: `500m CPU`, `512Mi memory`
  - base replicas: `0`
- Control plane endpoint mode: `public`
- Metrics Server generation flag: `enable_metrics_server=true` (used for cluster support, not KPI backend)

## Infrastructure Created (Names)
### OKE Clusters and Pools
- Cluster: `ca-ca-vs-kpo-20260420t15461`
  - Worker pools:
    - `ca-baseline`
    - `ca-benchmark-autoscaled` (min `0`, max `10`)
- Cluster: `kpo-ca-vs-kpo-20260420t15461`
  - Worker pool:
    - `kpo-baseline`

### Karpenter Resources
- Namespace: `karpenter`
- `OCINodeClass`: `benchmark-ocinodeclass`
- `NodePool`: `benchmark-nodepool`
  - limits: `cpu=40`, `memory=160Gi`
  - disruption: `WhenEmpty`, `consolidateAfter=5m`
  - requirements:
    - `karpenter.sh/capacity-type=on-demand`
    - `oci.oraclecloud.com/instance-shape=VM.Standard.E5.Flex`

### Benchmark Workload Resources
- Namespace: `benchmark-ca`
  - Deployment: `benchmark-load`
  - nodeSelector:
    - `benchmark.oracle.com/autoscaler=ca`
    - `benchmark.oracle.com/role=benchmark`
- Namespace: `benchmark-kpo`
  - Deployment: `benchmark-load`
  - nodeSelector:
    - `karpenter.sh/nodepool=benchmark-nodepool`

### KPO health highlights
- Controller namespace: `karpenter`
- VCN native CNI version: `3.2.0`
- `ipCount`: `16` (power-of-two gate passed)

## Scenario Matrix
| Profile | Target | Repeats | Up Replicas | Down Replicas | Hold (s) | Pending Timeout (s) | Ready Timeout (s) | ScaleDown Timeout (s) |
|---|---|---:|---:|---:|---:|---:|---:|---:|
| burst | ca | 3 | 20 | 0 | 120 | 300 | 1800 | 1800 |
| burst | kpo | 3 | 20 | 0 | 120 | 300 | 1800 | 1800 |
| steady | ca | 3 | 8 | 0 | 300 | 300 | 1800 | 1800 |
| steady | kpo | 3 | 8 | 0 | 300 | 300 | 1800 | 1800 |

Event taxonomy captured per run:
- `precheck_start`
- `scenario_start`
- `first_pod_pending`
- `first_capacity_ready`
- `all_running`
- `scale_down_complete`
- `scenario_end`

## Telemetry Collected
Primary backend: OCI Monitoring `oci_oke` namespace.  
Queries captured for both clusters:

- `UnschedulablePods`
- `NodeState`
- `KubernetesNodeCondition`
- `APIServerRequestCount`
- `APIServerResponseCount`

Additional evidence:
- Kubernetes events export
- CA and KPO controller logs
- CA autoscaler status configmap dump
- KPO snapshots (`nodepool`, `nodeclaims`, `ocinodeclass`)

Telemetry counts:
- metrics rows: `11935`
- metric queries: `10`
- k8s events rows: `0`
- controller log files: `3`
- snapshot files: `3`
- telemetry collection errors: `0`

## Results Observed
### Workload Volume
- Total runs: `12`
- Successful runs: `12`
- Total requested pods: `168`
  - burst/ca: `60`
  - burst/kpo: `60`
  - steady/ca: `24`
  - steady/kpo: `24`

### Aggregate KPIs (mean seconds)
| Profile | Target | Pending->First Capacity | Pending->All Running | Scale-Down Completion |
|---|---|---:|---:|---:|
| burst | ca | 124.357 | 184.862 | 690.667 |
| burst | kpo | 3.909 | 164.315 | 647.667 |
| steady | ca | 124.463 | 180.180 | 949.667 |
| steady | kpo | 3.918 | 165.798 | 471.667 |

### Per-Run Latency Detail
| Profile | Target | Repeat | Pods | Pending->First Capacity | Pending->All Running | Scale-Down Completion |
|---|---|---:|---:|---:|---:|---:|
| burst | ca | 1 | 20 | 145.5s | 200.9s | 688.0s |
| burst | ca | 2 | 20 | 107.6s | 164.3s | 695.0s |
| burst | ca | 3 | 20 | 120.0s | 189.4s | 689.0s |
| burst | kpo | 1 | 20 | 3.9s | 163.9s | 828.0s |
| burst | kpo | 2 | 20 | 3.9s | 164.5s | 540.0s |
| burst | kpo | 3 | 20 | 3.9s | 164.5s | 575.0s |
| steady | ca | 1 | 8 | 120.5s | 189.4s | 962.0s |
| steady | ca | 2 | 8 | 133.2s | 175.7s | 945.0s |
| steady | ca | 3 | 8 | 119.7s | 175.4s | 942.0s |
| steady | kpo | 1 | 8 | 3.9s | 162.7s | 465.0s |
| steady | kpo | 2 | 8 | 4.0s | 160.8s | 474.0s |
| steady | kpo | 3 | 8 | 3.8s | 173.9s | 476.0s |

### Unschedulable Pods (from OCI metric)
| Target | Unschedulable datapoints | Unschedulable duration | Max unschedulable pods |
|---|---:|---:|---:|
| ca | 17 | 17 minutes | 20 |
| kpo | 17 | 17 minutes | 20 |

### Controller Log Severity
| Target | Info | Warning | Error | Files |
|---|---:|---:|---:|---:|
| ca | 1370 | 4888 | 0 | 2 |
| kpo | 1293 | 0 | 8 | 1 |

## Data Quality Notes
- `k8s_events.jsonl` is empty in this run, so post-run event-derived per-pod lifecycle KPIs are not available.
- KPO `pending->first-capacity` in this harness can be satisfied by NodeClaim growth signal.
- Fine-grained pod lifecycle phases (`Scheduled`, `ContainerStarted`, `Ready`) would require live pod watch capture during scenario execution.

## Cleanup Outcome
Cell 15 executed and completed with `status=success`:
- KPO pre-destroy:
  - deleted `benchmark-nodepool`
  - waited for NodeClaims to reach 0
  - waited for nodepool-labeled nodes to reach 0
  - deleted `benchmark-ocinodeclass`
- Terraform destroy:
  - `tf/ca`: destroy rc=0, state empty
  - `tf/kpo`: destroy rc=0, state empty

Residual checks at cleanup time still showed existing OCI resources:
- both cluster OCIDs
- IAM artifacts:
  - `ca-workload-ca-vs-kpo-202604`
  - `kpo-nodes-ca-vs-kpo-202604`
  - `kpo-cluster-join-ca-vs-kpo-202604`
  - `kpo-workload-ca-vs-kpo-202604`

## Conclusion
For this run:

- KPO was much faster to first capacity signal than CA in both scenarios (~3.9s vs ~124s).
- KPO also reached all-running faster on average in both scenarios.
- Scale-down completion was faster for KPO, especially in steady profile (471.7s vs 949.7s).
- Unschedulable pod duration was identical between targets in this dataset.
- Interpretation caveat: this harness allows KPO first-capacity to be represented by NodeClaim growth, while CA uses benchmark node readiness growth; this should be kept explicit when presenting results.

## Key Artifacts
- `artifacts/ca-vs-kpo-20260420T154619Z/comparison_report.md`
- `artifacts/ca-vs-kpo-20260420T154619Z/reporting/scenario_timeline.csv`
- `artifacts/ca-vs-kpo-20260420T154619Z/reporting/execution_matrix.csv`
- `artifacts/ca-vs-kpo-20260420T154619Z/scenario_events_burst.jsonl`
- `artifacts/ca-vs-kpo-20260420T154619Z/scenario_events_steady.jsonl`
