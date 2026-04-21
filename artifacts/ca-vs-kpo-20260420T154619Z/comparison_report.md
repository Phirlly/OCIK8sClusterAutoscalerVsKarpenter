# CA vs KPO Comparison Report

Run ID: `ca-vs-kpo-20260420T154619Z`
Generated: `2026-04-21T01:02:46.235464Z`

## Scope
- Targets: `ca, kpo`
- Region: `us-ashburn-1`
- Scenario window: `2026-04-20T18:20:29.383648Z` to `2026-04-20T23:00:53.166354Z`

## What Was Executed
| Profile | Target | Repeats | Up Replicas | Down Replicas | Hold (s) | Pending Timeout (s) | Ready Timeout (s) | ScaleDown Timeout (s) |
|---|---|---:|---:|---:|---:|---:|---:|---:|
| burst | ca | 3 | 20 | 0 | 120 | 300 | 1800 | 1800 |
| burst | kpo | 3 | 20 | 0 | 120 | 300 | 1800 | 1800 |
| steady | ca | 3 | 8 | 0 | 300 | 300 | 1800 | 1800 |
| steady | kpo | 3 | 8 | 0 | 300 | 300 | 1800 | 1800 |

## Workload Volume
- Total scenario runs: `12`
- Successful runs: `12`
- Total requested pods across all runs: `168`
- `burst / ca` requested pods: `60`
- `burst / kpo` requested pods: `60`
- `steady / ca` requested pods: `24`
- `steady / kpo` requested pods: `24`

## Per-Run Timeline
| Profile | Target | Repeat | Scenario Start | First Pending | First Capacity | All Running | Scale Down Complete | End |
|---|---|---:|---|---|---|---|---|---|
| burst | ca | 1 | 2026-04-20T18:20:29.383648Z | 2026-04-20T18:20:37.755802Z | 2026-04-20T18:23:03.283045Z | 2026-04-20T18:23:58.643841Z | 2026-04-20T18:37:27.882246Z | 2026-04-20T18:37:27.884014Z |
| burst | kpo | 1 | 2026-04-20T18:37:27.884461Z | 2026-04-20T18:37:37.131158Z | 2026-04-20T18:37:40.998654Z | 2026-04-20T18:40:21.074620Z | 2026-04-20T18:56:10.961543Z | 2026-04-20T18:56:10.963012Z |
| burst | ca | 2 | 2026-04-20T18:56:10.963392Z | 2026-04-20T18:56:18.219277Z | 2026-04-20T18:58:05.787571Z | 2026-04-20T18:59:02.547388Z | 2026-04-20T19:12:38.914768Z | 2026-04-20T19:12:38.916541Z |
| burst | kpo | 2 | 2026-04-20T19:12:38.916727Z | 2026-04-20T19:12:48.795670Z | 2026-04-20T19:12:52.745556Z | 2026-04-20T19:15:33.290688Z | 2026-04-20T19:26:35.047458Z | 2026-04-20T19:26:35.050664Z |
| burst | ca | 3 | 2026-04-20T19:26:35.051091Z | 2026-04-20T19:26:42.151112Z | 2026-04-20T19:28:42.125942Z | 2026-04-20T19:29:51.522090Z | 2026-04-20T19:43:22.375541Z | 2026-04-20T19:43:22.378435Z |
| burst | kpo | 3 | 2026-04-20T19:43:22.378768Z | 2026-04-20T19:43:32.540724Z | 2026-04-20T19:43:36.451248Z | 2026-04-20T19:46:17.047163Z | 2026-04-20T19:57:53.985236Z | 2026-04-20T19:57:53.986544Z |
| steady | ca | 1 | 2026-04-20T21:01:27.723210Z | 2026-04-20T21:01:36.693356Z | 2026-04-20T21:03:37.194091Z | 2026-04-20T21:04:46.127495Z | 2026-04-20T21:25:49.933667Z | 2026-04-20T21:25:49.935077Z |
| steady | kpo | 1 | 2026-04-20T21:25:49.935271Z | 2026-04-20T21:26:01.394686Z | 2026-04-20T21:26:05.341578Z | 2026-04-20T21:28:44.105977Z | 2026-04-20T21:41:30.330078Z | 2026-04-20T21:41:30.330641Z |
| steady | ca | 2 | 2026-04-20T21:41:30.330913Z | 2026-04-20T21:41:37.208409Z | 2026-04-20T21:43:50.377897Z | 2026-04-20T21:44:32.880792Z | 2026-04-20T22:05:19.931927Z | 2026-04-20T22:05:19.932898Z |
| steady | kpo | 2 | 2026-04-20T22:05:19.933080Z | 2026-04-20T22:05:28.786186Z | 2026-04-20T22:05:32.820418Z | 2026-04-20T22:08:09.578997Z | 2026-04-20T22:21:05.441429Z | 2026-04-20T22:21:05.442001Z |
| steady | ca | 3 | 2026-04-20T22:21:05.442216Z | 2026-04-20T22:21:12.139482Z | 2026-04-20T22:23:11.857400Z | 2026-04-20T22:24:07.571795Z | 2026-04-20T22:44:51.636241Z | 2026-04-20T22:44:51.637341Z |
| steady | kpo | 3 | 2026-04-20T22:44:51.637569Z | 2026-04-20T22:45:01.804822Z | 2026-04-20T22:45:05.578540Z | 2026-04-20T22:47:55.694383Z | 2026-04-20T23:00:53.165281Z | 2026-04-20T23:00:53.166354Z |

## Per-Run Latency Detail
| Profile | Target | Repeat | Pods (Up) | Pending->First Capacity | Pending->All Running | Start->All Running | AllRunning->ScaleDownComplete | Scenario Total | Reported ScaleDown Elapsed |
|---|---|---:|---:|---:|---:|---:|---:|---:|---:|
| burst | ca | 1 | 20 | 145.5s | 200.9s | 209.3s | 809.2s | 1018.5s | 688.0s |
| burst | ca | 2 | 20 | 107.6s | 164.3s | 171.6s | 816.4s | 988.0s | 695.0s |
| burst | ca | 3 | 20 | 120.0s | 189.4s | 196.5s | 810.9s | 1007.3s | 689.0s |
| burst | kpo | 1 | 20 | 3.9s | 163.9s | 173.2s | 949.9s | 1123.1s | 828.0s |
| burst | kpo | 2 | 20 | 3.9s | 164.5s | 174.4s | 661.8s | 836.1s | 540.0s |
| burst | kpo | 3 | 20 | 3.9s | 164.5s | 174.7s | 696.9s | 871.6s | 575.0s |
| steady | ca | 1 | 8 | 120.5s | 189.4s | 198.4s | 1263.8s | 1462.2s | 962.0s |
| steady | ca | 2 | 8 | 133.2s | 175.7s | 182.5s | 1247.1s | 1429.6s | 945.0s |
| steady | ca | 3 | 8 | 119.7s | 175.4s | 182.1s | 1244.1s | 1426.2s | 942.0s |
| steady | kpo | 1 | 8 | 3.9s | 162.7s | 174.2s | 766.2s | 940.4s | 465.0s |
| steady | kpo | 2 | 8 | 4.0s | 160.8s | 169.6s | 775.9s | 945.5s | 474.0s |
| steady | kpo | 3 | 8 | 3.8s | 173.9s | 184.1s | 777.5s | 961.5s | 476.0s |

## KPI Aggregate Summary
| Profile | CA Pending->First Capacity | KPO Pending->First Capacity | CA Pending->All Running | KPO Pending->All Running | CA Scale-Down Complete | KPO Scale-Down Complete |
|---|---:|---:|---:|---:|---:|---:|
| burst | 124.4s | 3.9s | 184.9s | 164.3s | 690.7s | 647.7s |
| steady | 124.5s | 3.9s | 180.2s | 165.8s | 949.7s | 471.7s |

## Controller Log Severity
| Target | Info | Warning | Error | Log Files |
|---|---:|---:|---:|---:|
| ca | 1370 | 4888 | 0 | 2 |
| kpo | 1293 | 0 | 8 | 1 |

## Data Quality and Caveats
- Kubernetes events available: `False`
- `k8s_events.jsonl` is empty for this run, so event-derived pod lifecycle KPIs are unavailable post-run.
- KPO pending->first-capacity uses NodeClaim growth signal in this harness.
- For per-pod lifecycle (Scheduled/ContainerStarted/Ready), collect live pod watch data during scenario execution (next run enhancement).
