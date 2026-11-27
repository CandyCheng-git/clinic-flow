# Performance Benchmark Plan and Report

Version: 0.5 | Updated: 27 November 2026

Status: Not run — measurement template

## Current result

**Not run.** No latency improvement, percentage saving or p95 result is claimed.

## Proposed target and workload

Warm primary read APIs should target p95 below 500 ms under an explicitly declared workload. First measure customer search, daily staff calendar, customer voucher history and Manager summary separately. Record cold starts separately if optional hosting introduces them. Do not blend unlike operations into one flattering percentile.

Use the portfolio-scale synthetic fixture as a starting target: 1,000 customers, 8 staff, 10,000 bookings, 1,500 purchases and 5,000 ledger entries. Verify generated counts and correctness before measuring. SRS proposes 5 concurrent authenticated virtual users, no think time, 60-second excluded warm-up then 5 measured minutes per endpoint, extending to >=1,000 measured requests. Search/history page 50; Manager summary 7 clinic dates. Measure local client request-start to full-response elapsed time; retain hardware/build/database/network/cache/fixture details and all errors. Per endpoint target: p95 <500 ms, zero unexpected errors. These are review targets, not executed results.

## Measurement record

| Field | Required value |
| --- | --- |
| Run ID / date / operator | Not recorded |
| Commit / release build / configuration | Not recorded |
| OS, CPU, RAM and runtime versions | Not recorded |
| Database version, indexes and settings | Not recorded |
| Network path and host characteristics | Not recorded |
| Generator version, seed, reference date, exact row counts | Not recorded |
| Endpoint, input parameters and expected response | Not recorded |
| Cache / connection warm-up and cold-start policy | Not recorded |
| Users or request rate, duration and repetitions | Not recorded |
| Successful samples and failures by status | Not recorded |
| Median / p95 / throughput / error rate | Not measured |
| Raw results and analysis path | Not available |

## Procedure

Verify response correctness, fix operation/input/environment, warm up under the declared policy, execute the planned workload, retain raw measurements and record errors/timeouts. Compare like-for-like runs with enough representative samples. Preserve baseline and changed commits. Investigate outliers and load-generator saturation rather than dropping inconvenient samples.

After a specific optimization, report its scope and trade-offs. A synthetic local benchmark may support a claim about that benchmark. It cannot retrospectively establish improvements in earlier employment or predict clinic business savings.

## Result table for future runs

| Run | Operation / workload | Samples | Median ms | p95 ms | Error rate | Evidence |
| --- | --- | --- | --- | --- | --- | --- |
| Not run | Not set | — | — | — | — | None |

Mode boundary: this benchmark targets the real API/PostgreSQL workload. Public MSW/IndexedDB timing may be recorded separately as UI simulation behavior but cannot populate real-API p95/throughput or SQL performance results. Record mode and artifact/commit with each run.
