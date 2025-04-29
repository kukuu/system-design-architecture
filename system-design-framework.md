# System Design Framework

_Case Study for online travel:_

## Clarifying Questions & Assumptions

**- Traffic Volume:**
i. Are we handling 10K or 10M requests/sec? (Assume 50K RPS peak)

ii. What’s the read:write ratio? (Assume 80:20 for Search vs. Booking)

**- Data Characteristics:**

i. What’s the avg. payload size for Pricing/Booking? *(Assume 2KB/req)*

ii. Do Reviews need real-time indexing? (Yes for recent reviews?)

**- Global Requirements:**

i. Latency SLA? (<200ms for Search, <1s for Booking)

ii. Data consistency level? (Strong for Booking, eventual for Reviews)

## Key PILLARS in the Architecture (SSREFOS)

- Scalability:
Independent scaling of microservices ensures that services can handle their unique workloads without impacting others.

- Speed

- Resilience:
Asynchronous communication and circuit breakers mitigate the risk of cascading failures.

- Flexibility:
A modular approach allows for iterative development, making introducing new features or updating existing ones easier.

- Observability: Centralized logging and monitoring enable debugging and performance analysis for distributed services.

- Security: API Gateway and token-based authentication (OAuth2) ensure a secure entry.

## Components

![image](https://github.com/user-attachments/assets/3180a599-56bf-4b10-9b81-fa43c0384f0a)


![image](https://github.com/user-attachments/assets/eacba6c4-34f3-4743-ab27-2629509d7304)

## Key Components (Frontend & Backend):

- Client Application (React, Next.js):

i. CSR (Client-Side Rendering - Loading on demand) and SSR (Server-Side Rendering - Static compiled  data) hybrid model for fast loading. 

ii. Tailwind CSS for styling and responsive design. 

- State Management
i. REDUX/Context API/MobX for centralised store: Action /Reducers

ii. Data Fetch: GraphQL/AXIOS

iii. Cache: RTK, SWR, Session storage, local storage

iv. Control Logic for handling EVENTS and ACTIONS

- API Gateway:

i. Unified entry point for API calls. 

ii. Load balancer for distributing traffic. 

iii. Handles authentication, rate limiting (IP targeting), and CORS. 

IV. Schema validation- JSON/TypeScript

- Service Communication Layer:

i. GraphQL for flexible query structures. 

ii. REST API fallback for legacy services. 

iii. WebSockets for real-time updates (e.g., booking status). 

- Authentication and Authorization (Auth Service):

i. OAuth 2.0 (User Identity) and JWT for secure token-based access. 

ii. Role-based access control (RBAC). 

iii. XSS and CSRF prevention.

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

## Component Breakdown:

### Frontend

- UI Layer:

i. Component Library (Storybook): Standardized UI components. 

ii. Pages:
Home, Search Results, Booking Flow, Pricing Details, and Reviews. 

iii. Reusable Components:
Buttons, forms, and modals. 

iv. State Management (Redux/RTK Query):

a. Local State: UI state (e.g., modal visibility). 

b. Global State: Cached API responses and shared data. 

c: Middleware: Redux Thunk or Redux Saga for handling side effects like asynchronous calls. 

d: Controller Layer: 

i. API Service Handlers: Abstracted layer for handling API requests (GraohQL,fetch, axios). 

ii. Event Handling: WebSockets for real-time updates (e.g., bookings and reviews).

e. Data Layer:

i. Caching Strategies: SWR (stale-while-revalidate) for optimized data fetching. 

ii. Schema Validation: JSON Schema or TypeScript interfaces. 

## . Challenges and Resolutions:
- Challenge 1: Scalability

i. 
Issue: Handling millions of concurrent requests. 

Resolution: Implement CDN, Redis caching, and Kubernetes autoscaling. 

- Challenge 2: Real-Time Updates

Issue: Booking and pricing require real-time data. 

Resolution: Use WebSockets and Event-Driven Architecture (Microsercices, KAFKA) with message queues RabbitMQ. 

- Challenge 3: Observability

Issue: Monitoring user interactions and failures. 

Resolution: Integrate Prometheus and ELK for real-time metrics and logs. 

- Challenge 4: Security

Issue: Authentication, data breaches, and fraud prevention. 

Resolution: Role-based access, OAuth2/JWT, encryption (TLS), and WAF. 

## Testing and Validation:

- Unit Testing:
i. Jest and React Testing Library. 

- Integration Testing:
i. Cypress for end-to-end testing. 

ii. Performance Testing: Load tests with JMeter. 

iii. CI/CD Tests: Automated test pipelines with GitHub Actions/Jenkins.


## Efficiency and Scalability Considerations

**_Data Structures:_**

i. Use B-Trees and Hash Indexes for database indexing (B-Trees are balanced tree data structures used for ordered data storage and efficient range queries, while Hash Indexes use hash tables for fast, exact-match lookups but do not support range queries). 

ii. Utilize Bloom Filters in search services for fast lookups. 

iii. Implement Trie Structures for autocomplete and search suggestions. 

**_Size Estimations:_**

i. Plan database sharding for horizontal scaling from the start.

ii. Estimate peak loads using traffic simulation tools (e.g., Locust, JMeter). 

**_Production Testing:_**

**i. Perform Load Testing using Apache JMeter and Gatling.**

ii. Monitor performance metrics and optimize SQL queries. 

iii. Simulate Fault Injection Testing using Chaos Engineering tools (e.g., Gremlin). 

**_Observability Tests (How do you know if the deploymeant in production works - using Prometheus , ELK, GRAFANA)_**

i. Validate metrics and logs collection.

ii. Implement alerts for anomalies and latency spikes. 

**_Security Tests:_**

i. Conduct Penetration Testing and Vulnerability Scans (SONARQ). SENTRY for code smell.

ii. Ensure proper encryption (TLS) and secure secrets management.


## Types of System Design Architectures

**Monolithic Architecture:**

i. All functionality is bundled into a single codebase.

ii. Suitable for small teams or early-stage startups but lacks flexibility and scalability.

**Service-Oriented Architecture (SOA):**

i. Uses a shared bus for communication between services, unlike microservices, which prefer lightweight communication (e.g., REST, gRPC).

**Event-Driven Architecture:**

i. Services communicate by exchanging events via message brokers like Kafka /RbbitMQ.

ii. Useful for systems that require asynchronous and real-time interactions.

**Serverless Architecture:**

i. Functions are deployed independently, and scaling is managed automatically by the cloud provider.

ii. Ideal for reducing infrastructure management.

**Microservices Architecture:**

i. Services are independent, use APIs or message brokers for communication, and scale independently.

ii. Best for large, distributed systems like Skyscanner, where scalability, modularity, and resilience are paramount.


## Core Types of Data Structures

**Key-Value Pairs**

i. Use Case: Ideal for caching or storing session data, configuration settings, or any data that can be accessed with a unique key.

ii. Example:

Tools: Redis, DynamoDB.

Scenario: Caching frequently accessed flight search results in Skyscanner.
Processing: Retrieve and update values based on keys efficiently (O(1) operations).

**JSON / BSON Documents**

i. Use Case: Used for unstructured or semi-structured data in document stores.

ii. Example:

Tools: MongoDB, CouchDB.

Scenario: Storing user travel preferences or booking details.

Processing: Query and modify specific fields in the document, such as retrieving all bookings for a user.

**Relational Tables**

i. Use Case: For structured data requiring relationships between entities.

ii. Example:
Tools: PostgreSQL, MySQL.

Scenario: Managing user accounts, bookings, and payments.

Processing: Perform SQL operations like SELECT, JOIN, and GROUP BY to fetch or aggregate data.

**Graphs**

i. Use Case: For relationships and connections between entities.

ii. Example:

Tools: Neo4j, ArangoDB.

Scenario: Representing and querying travel routes or user connections.

Processing: Use graph traversal algorithms like Depth-First Search (DFS) or Breadth-First Search (BFS) for recommendations or shortest path discovery.

**Queues**

i. Use Case: For message passing and asynchronous processing.

ii. Example:
Tools: RabbitMQ, Amazon SQS, Kafka.

Scenario: Processing price alerts or booking notifications in Skyscanner.

Processing: Messages are enqueued by producers and dequeued by consumers for further handling.


**Logs**

i. Use Case: For sequential, append-only data.

Example:

Tools: Kafka, Elasticsearch.

Scenario: Capturing user activity logs or transaction histories.
Processing: Use batch processing or streaming frameworks (e.g., Apache Flink, Spark) to analyze logs in real-time.


**Distributed Hash Tables**

i. Use Case: For distributed storage of key-value data across nodes.

ii. Example:

Tools: Cassandra, DynamoDB.

Scenario: Handling distributed storage of travel deals or search indexes.

Processing: Partition and replicate data across nodes using consistent hashing.


**Blob Storage**

i. Use Case: For storing unstructured data like images, PDFs, or videos.

ii. Example:

Tools: Amazon S3, Azure Blob Storage.

Scenario: Storing user-uploaded passport images or itineraries.

Processing: Retrieve, update, or delete blobs as needed.


## Unified e2e architecture

![image](https://github.com/user-attachments/assets/16e004e4-5c79-48eb-9fa4-8ddfe4af2c07) 

## Data Flow


**Initial Request:**

i. User interacts with Next.js/React app → Redux dispatches action

ii. RTK Query/SWR checks cache → If stale/missing, fires GraphQL/Axios request

**Authentication:**
i. Auth0/OAuth tokens attached to requests via Axios interceptors

ii. API Gateway validates JWT via Ory Hydra before routing

**API Gateway Processing:**

i. Kong routes to appropriate service (Search/Pricing/Booking)

ii Rate limiting applied per client IP

iii. Schema validation rejects malformed requests

**Service Interaction:**

i. Search: GraphQL queries hit Elasticsearch via Apollo Server

ii. Booking: Axios REST calls trigger PostgreSQL transactions

iii. Real-time Pricing: WebSocket connection to Kafka streams

**Response Handling:**

i. Successful responses cached in Redux/SWR

ii. Errors captured by Sentry with user context

iii. MSW intercepts requests in dev/staging environments

**UI Update:**

i. Tailwind/Material UI components re-render with new data

ii. Loading states managed by RTK Query/SWR hooks

iii. Persistent state (e.g., auth) stored securely via Keychain (iOS) or cookies (web)

## Updated Architecture with Microservice modules

![image](https://github.com/user-attachments/assets/f1a9f119-1e10-4522-93be-41908eb9a384)

## Architecture sketches:
1. https://miro.com/app/board/uXjVPk8w1hA=/

2. https://miro.com/app/board/uXjVI9Uvva0=/


