---
title: auto-configurator
img_path: /assets/img/post/
---

# Introduction.
auto-configurator is a service to configure rate-limits for other production services.  

# Scenarios.
1. When callee service wants to have controls over caller's rps.  
- we can prevent caller from overloading callee service. e.g. DDOS.
2. When callee service wants to have controls over endpoint's rps.
- Some endpoints have hundreds of callers and it's hard to have controls over all the callers.

# How it works. 
![Overview](2024-04-04-rate-limit-configurator-how.png)  
## We have different policies.  
### policy based on callers.
We apply some buffer for the average rps from callers. 
For example, if average rps for callerA is 50 and we apply 100% buffer on top of that, we set the rate-limits to 100. (50 * ( 1 + 1 )) = 100.
### policy based on endpoints.
Similar to callers' policy, we set rate-limits on endpoint level.  
### Some other polies.
We can set rate-limits with caller/endpoint combined.  

# Design.  
![Architecture](2024-04-04-Architecture.png)
#### Two ways to trigger rate-limits generation.
1. users modify yaml file manually.  
2. `if time.Now() - LastSuccessTime < Frequency`.  

#### synchronization failure.
1. if **hash** isn't matched.  
2. if **LastSuccessTime** is stale. 

# Monitor
1. Rate-limits change dramatically.  
2. For a given caller, the number of buckets shouldn't change.
- We create an alert based on the number of buckets.  
3. Latency.  
4. ... 



