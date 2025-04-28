# System Design Framework

_Case Study for online travel:_

## Clarifying Questions & Assumptions

- Traffic Volume:
i. Are we handling 10K or 10M requests/sec? (Assume 50K RPS peak)

i. What’s the read:write ratio? (Assume 80:20 for Search vs. Booking)

- Data Characteristics:

i. What’s the avg. payload size for Pricing/Booking? *(Assume 2KB/req)*

ii. Do Reviews need real-time indexing? (Yes for recent reviews)

- Global Requirements:

i. Latency SLA? (<200ms for Search, <1s for Booking)

ii. Data consistency level? (Strong for Booking, eventual for Reviews)

## Key PILLARS in the Architecture (SSREFOS)

- Scalability:
Independent scaling of microservices ensures that services can handle their unique workloads without impacting others.
Speed

- Resilience:
Asynchronous communication and circuit breakers mitigate the risk of cascading failures.

- Flexibility:
A modular approach allows for iterative development, making introducing new features or updating existing ones easier.
Observability: Centralized logging and monitoring enable debugging and performance analysis for distributed services.
Security: API Gateway and token-based authentication (OAuth2) ensure a secure entry 
