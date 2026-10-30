# K6 lab results 
  
## Part 0 
- Step 0.1, http_req_duration: 
- Step 0.2, requests, rate and p(95), and why the rate stays below 5: 
- Step 0.6, exit code of the normal run and of the run with P95=300:
0 and 99
- Step 0.7, rules that passed, p(95), p(99), exit code after your change: 
99
- Step 0.8, what was wrong, and how the checks showed it: Case-sensitive ตัว P->p, K->k; นั่งดูใน explore.js
  
## Part A results 
| Step | Test type    | Users | Req/s | p(95) | Max | Failed | Checks | 
| ---- | ------------ | ----- | ----- | ----- | --- | ------ | ------ | 
| A1   | Smoke        | 1     |       |       |     |        |        | 
| A2   | Average load | 3     |       |       |     |        |        | 
| A3   | Stress       | 5     |       |       |     |        |        | 
| A4   | Spike        | 5     |       |       |     |        |        | 
| A5   | Soak         | 2     |       |       |     |        |        | 
  
## Part B 
1. 
2. 
3. 
4. 
5. 
6. 
7. 
8. 
9.