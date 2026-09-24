# Performance JMeter Suite (ReqRes)

[![JMeter Performance](https://github.com/SatyamChouksey-88/performance-jmeter-suite/actions/workflows/jmeter.yml/badge.svg)](https://github.com/SatyamChouksey-88/performance-jmeter-suite/actions/workflows/jmeter.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

**[▶ Live HTML dashboards](https://satyamchouksey-88.github.io/performance-jmeter-suite/)**

JMeter CLI load / stress / spike plans against the public [ReqRes](https://reqres.in) API, run in GitHub Actions, with HTML dashboards published to GitHub Pages.

## Usage policy (checked before load)

ReqRes documents a **free-tier budget of ~250 requests/day** per API key after its 2025 relaunch, and returns **429** when exceeded ([apis.io rate-limits summary](https://apis.io/rate-limits/reqres/reqres-rate-limits/), [ReqRes QA guide](https://reqres.in/blog/qa-automation-with-reqres)). This suite keeps peak concurrency **≤ 25 users** (well under 50), short loops, and a **300 ms think time** so a full CI run stays a small fraction of the daily budget.

## Plans

| Plan | Threads | Ramp | Loops | Intent |
|---|---|---|---|---|
| `plans/load-test.jmx` | 5 | 10 s | 5 | Steady modest load |
| `plans/stress-test.jmx` | 20 | 30 s | 3 | Push toward higher concurrency |
| `plans/spike-test.jmx` | 25 | 2 s | 2 | Short spike |

CSV-driven page query: `data/pages.csv`. Assertions: HTTP 200 + duration &lt; 2000 ms.

## Local run

```bash
jmeter -n -t plans/load-test.jmx -l results/load.jtl -e -o report/load
```

Or with Docker (same image as CI):

```bash
docker run --rm -v "$PWD:/tests" -w /tests justb4/jmeter:5.5 \
  -n -t plans/load-test.jmx -l results/load.jtl -e -o report/load
```

## CI / Pages

Workflow [`.github/workflows/jmeter.yml`](.github/workflows/jmeter.yml) runs all three plans, writes `results/summary.json`, and publishes dashboards to `gh-pages`.

## Results

From CI run on **2026-09-24** (`summary.json` artifact / Pages). Peak concurrency **25 users**; **0% errors** across all three plans.

| Plan | Samples | Avg (ms) | p95 (ms) | Max (ms) | Error % |
|---|---|---|---|---|---|
| Load (5 users) | 25 | 66.6 | 97 | 546 | 0.0 |
| Stress (20 users) | 60 | 49.8 | 91 | 340 | 0.0 |
| Spike (25 users) | 50 | 70.3 | 194 | 359 | 0.0 |

Interpretation: at this modest scale against ReqRes, p95 stayed under **200 ms** even on the spike plan; no HTTP failures. These numbers are evidence of the harness, not a claim about ReqRes production capacity.

## Author & license

**Satyam Chouksey** — QA Automation Engineer / SDET · MIT
