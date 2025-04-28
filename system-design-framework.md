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
Security: API Gateway and token-based authentication (OAuth2) ensure a secure entry.

## Components

![image](https://github.com/user-attachments/assets/3180a599-56bf-4b10-9b81-fa43c0384f0a)


![image](https://github.com/user-attachments/assets/eacba6c4-34f3-4743-ab27-2629509d7304)

## Key Components in the Frontend High-Level Design:

- Client Application (React, Next.js):

i. CSR (Client-Side Rendering - Loading on demand) and SSR (Server-Side Rendering - Static compiled  data) hybrid model for fast loading. 

ii. Tailwind CSS for styling and responsive design. 

- API Gateway:

i. Unified entry point for API calls. 

ii. Load balancer for distributing traffic. 

iii. Handles authentication, rate limiting, and CORS. 

- Service Communication Layer:

i. GraphQL for flexible query structures. 

ii. REST API fallback for legacy services. 

iii. WebSockets for real-time updates (e.g., booking status). 

- Authentication and Authorization (Auth Service):

i. OAuth 2.0 (User Identity) and JWT for secure token-based access. 

ii. Role-based access control (RBAC). 

- Data Caching and CDN (Content Delivery Network):

i. Redis or Varnish for caching frequently accessed data (kEY/VALUES). 

ii. CDN (e.g., Cloudflare) for static asset delivery. 

- Logging and Monitoring (Observability):

i. Prometheus for metrics collection. 

ii. ELK Stack (Elasticsearch, Logstash, Kibana) for centralized logging and visual dashboards. 

- CI/CD Pipeline:

i. GitHub Actions / Jenkins for automated builds, tests, and deployments. 

ii. Canary deployments for rolling out updates with minimal risk. 

- Containerization and Orchestration:

i. Docker for containerization, and migration to environments exporting consistent configurations for stabilisation (dev/qa/sataging) 

ii. Kubernetes for container orchestration, scaling, and failover. 


