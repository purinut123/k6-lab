# K6 performance-testing lab

This repository contains the Part 0 exercises, the shared QuickPizza user journey, and smoke, average-load, stress, spike, and soak tests from the ITDS362 K6 lab.

## Requirements

- K6 v2.2.0 or later (results in `RESULTS.md` were produced with v2.3.0)
- Internet access to `https://quickpizza.grafana.com`

QuickPizza is a shared public demo. Do not raise these scripts above five VUs. The soak test is intentionally limited to two VUs.

## Run the tests

Run an individual script from the repository root, for example:

```sh
k6 run 07-requirement.js
k6 run --vus 5 --duration 20s 08-bug-hunt.js
```

Generate the Part A HTML reports with:

```sh
k6 run --out web-dashboard=export=reports/a2-average.html a2-average.js
k6 run --out web-dashboard=export=reports/a3-stress.html a3-stress.js
k6 run --out web-dashboard=export=reports/a4-spike.html a4-spike.js
k6 run --out web-dashboard=export=reports/a5-soak.html a5-soak.js
```

Measured results, analysis, and answers to Part B are in [RESULTS.md](RESULTS.md). Generated dashboards are in [`reports/`](reports/).
