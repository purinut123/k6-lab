# K6 lab results

Tests were run against `https://quickpizza.grafana.com` on 8 November 2026 with K6 v2.3.0. Because QuickPizza is a shared public service reached over the internet, a later run can produce different timings.

## Part 0

- **Step 0.1:** `http_req_duration` was 171.20 ms.
- **Step 0.2:** 95 requests, 4.63 requests/s, and p(95) 191.40 ms. The rate stayed below 5 requests/s because each VU waits for the HTTP response and then sleeps for one second before starting its next iteration.
- **Step 0.3:** The run completed exactly 20 iterations, shared by five VUs.
- **Step 0.4:** All three API checks passed.
- **Step 0.5:** The request returned the expected 401; the check passed and `http_req_failed` remained 0.00%.
- **Step 0.6:** The normal run exited with code 0. The earlier `P95=300` run recorded in this repository exited with code 99. On a verification rerun, the public service was faster (p(95) 155.99 ms), so the same 300 ms threshold exited with 0; this difference is expected for a shared internet service.
- **Step 0.7:** All rules passed: p(95) 197.45 ms was below 1,000 ms, p(99) 256.30 ms was below 1,500 ms, the request failure rate was 0.00%, and checks were 100%. Changing the p(95) limit from 1,000 ms to 100 ms made the run fail with exit code 99.
- **Step 0.8:** The API's ingredient and tool names are case-sensitive. The original request used `pepperoni` and `knife`, but the API values are `Pepperoni` and `Knife`, so the exclusions were ignored. Before the fix, only 80.22% of checks passed: the pepperoni check failed 17 times and the knife check failed 37 times. After fixing the case in the request and checks, all 270 checks passed. Repeated checks under load sampled many randomly selected pizzas; one manual request might happen to return a pizza without either excluded item and hide the bug.

## Part A results

| Step | Test type | Users | Req/s | p(95) | Max | Failed | Checks |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: |
| A1 | Smoke | 1 | 0.60/s | 158.60 ms | 161.49 ms | 0.00% | 100.00% |
| A2 | Average load | 3 | 1.48/s | 173.96 ms | 217.76 ms | 0.00% | 100.00% |
| A3 | Stress | 5 | 2.46/s | 153.03 ms | 215.74 ms | 0.00% | 100.00% |
| A4 | Spike | 5 | 1.13/s | 160.05 ms | 307.86 ms | 0.00% | 100.00% |
| A5 | Soak | 2 | 1.09/s | 172.07 ms | 340.16 ms | 0.00% | 100.00% |

## Part B

1. The results for A1 to A5 are shown in the table above. All five tests passed their thresholds.

2. Five VUs do not mean five requests every second. A VU must wait for the response and then execute `sleep(1)` before its next iteration, so an iteration takes more than one second and the combined rate stays below 5 requests/s.

3. Response time did not grow from the average-load test to the stress test. The overall p(95) changed from 173.96 ms at 3 VUs to 153.03 ms at 5 VUs. These small tests are well inside QuickPizza's operational envelope, so the service still has spare capacity and ordinary network/shared-service variation is larger than the effect of two extra VUs. This does not establish where the edge of the envelope is.

4. There was no meaningful response-time increase during the spike. Excluding the initial connection warm-up, p(95) was about 156.55 ms before the spike and 160.16 ms during the 5-VU hold. During the first 10 seconds after dropping the load, p(95) was 158.69 ms, so no recovery delay was observable; it was back at the earlier level within the first 10-second observation window.

5. The soak test showed no sustained degradation. During the first two minutes of the constant-load period, the average was 85.88 ms, p(95) was 172.09 ms, and the failure rate was 0%. During the final two minutes, the average was 84.37 ms, p(95) was 182.13 ms, and the failure rate was still 0%. The small p(95) difference is not accompanied by a higher average or errors. A several-hour soak test could reveal a memory or connection leak that gradually exhausts resources.

6. An average can hide a slow tail. A p(95) requirement states how quickly 95% of requests complete and exposes the experience of slower users more clearly than the mean.

7. First, a lab network can be local and controlled, while this run includes changing internet latency, routing, and packet loss. Second, a lab system can have dedicated, known hardware and traffic, while QuickPizza is shared with unknown users and may have different capacity, deployment, caches, or background load.

8. A large unauthorized stress, spike, or breakpoint test can exhaust the site's resources and become a denial-of-service event, harming its owner and other users. It is unethical and may violate service terms or the law, so high-load testing requires ownership or explicit written permission.

9. Vague requirement: “My social media feed should load quickly.” Testable requirement: “With 10 concurrent users for five minutes, 95% of feed requests complete within 1,000 ms, 99% complete within 1,500 ms, and fewer than 1% fail.” The K6 thresholds are:

   ```javascript
   thresholds: {
     http_req_duration: ['p(95)<1000', 'p(99)<1500'],
     http_req_failed: ['rate<0.01'],
   }
   ```
