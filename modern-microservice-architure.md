# Personalised Product Recommendations Architecture

## MACH-based Cloud Native Solution for Specsavers

**MACH = Microservices + API-first + Cloud-native + Headless**
  - Microservices: Independent, single-purpose services

  - API-first: All functionality exposed via APIs
  - Cloud-native: SaaS, scalable, resilient
  - Headless: Frontend decoupled from backend

## Clarification Questions

- What are the primary business goals for this feature - conversion uplift, AOV increase, or customer retention?

- How will this system integrate with existing Specsavers systems like e-commerce, CRM, and inventory management?

- What are the data privacy requirements and GDPR compliance needs for handling customer data?

- What is the recommended placement strategy for recommendations across homepage, product pages, cart, and email?

- What are the budget and timeline constraints for implementing this personalized recommendation system?

## Key PILLARS 

- Scalability: Independent scaling of microservices ensures that services can handle their unique workloads without impacting others.
- Speed: Optimized data flows and caching strategies enable real-time recommendations and fast response times.
- Resilience: Asynchronous communication and circuit breakers mitigate the risk of cascading failures.
- Flexibility: A modular approach allows for iterative development, making introducing new features or updating existing ones easier.
- Observability: Centralized logging and monitoring enable debugging and performance analysis for distributed services.
- Security: API Gateway and token-based authentication (OAuth2) ensure a secure entry point.

## Benefits for Specsavers:

- Marketing teams can quickly update promotional banners for frames, lens offers, or seasonal campaigns

- Consistent content across all channels (web, mobile, in-store)

- A/B testing of promotional content alongside product recommendations

- Separation of content management from application logic

   


## Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           SPECSAVERS RECOMMENDATION PLATFORM                │
│                        Microservices Architecture - MACH Based              │
└─────────────────────────────────────────────────────────────────────────────┘

┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
│   WEB APP   │  │ MOBILE APP  │  │ IN-STORE    │  │   EMAIL     │
│             │  │             │  │ TABLETS     │  │  SERVICE    │
└──────┬──────┘  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘
       │                │                │                │
       └─────────────────────────────────┼────────────────┘
                                         │
                                  ┌──────▼──────┐
                                  │ API GATEWAY │
                                  │ ┌─────────┐ │
                                  │ │ GraphQL │ │
                                  │ │  REST   │ │
                                  │ │ Auth    │ │
                                  │ │ Rate    │ │
                                  │ │ Limiting│ │
                                  │ └─────────┘ │
                                  └──────┬──────┘
                                         │
    ┌────────────────────────────────────┼───────────────────────────────────┐
    │                MICROSERVICES LAYER                                     │
    │                                                                        │
    │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐   │
    │  │   USER      │  │  PRODUCT    │  │RECOMMENDATION│  │ BEHAVIOR    │   │
    │  │  PROFILE    │  │  CATALOG    │  │   ENGINE    │  │ TRACKING    │   │
    │  │ SERVICE     │  │ SERVICE     │  │             │  │ SERVICE     │   │
    │  │             │  │             │  │             │  │             │   │
    │  │• Preferences│  │• Inventory  │  │• Real-time  │  │• Clickstream│   │
    │  │• History    │  │• Attributes │  │  Inference  │  │• Sessions   │   │
    │  │• Demographics│ │• Pricing    │  │• ML Models  │  │• Events     │   │
    │  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘   │
    │         │                │                │                │          │
    └─────────┼────────────────┼────────────────┼────────────────┼──────────┘
              │                │                │                │
    ┌─────────▼────────────────▼────────────────▼────────────────▼──────────┐
    │                         DATA & AI/ML LAYER                            │
    │                                                                       │
    │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  │
    │  │  USER       │  │  PRODUCT    │  │   VECTOR    │  │  EVENT      │  │
    │  │ PROFILES    │  │ CATALOG DB  │  │  DATABASE   │  │ STREAMING   │  │
    │  │  DATABASE   │  │             │  │             │  │   (Kafka)   │  │
    │  │             │  │• PostgreSQL │  │• Pinecone   │  │             │  │
    │  │• MongoDB    │  │• Elastic    │  │• Weaviate   │  │• Real-time  │  │
    │  │• Redis      │  │• Search     │  │• Embeddings │  │  Processing │  │
    │  └─────────────┘  └─────────────┘  └─────────────┘  └──────┬──────┘  │
    │                                  ┌─────────────┐           │         │
    │  ┌─────────────┐                │  HEADLESS   │           │         │
    │  │   AI/ML     │                │     CMS     │           │         │
    │  │  PLATFORM   │                │             │           │         │
    │  │             │                │• Contentful │           │         │
    │  │• Training   │                │• Storyblok  │           │         │
    │  │• Inference  │                │• Content    │           │         │
    │  │• Monitoring │                │  Fragments  │           │         │
    │  └─────────────┘                │• Promotions │           │         │
    │                                 └──────┬──────┘           │         │
    │                                        │                  │         │
    │                                  ┌─────▼──────────────────▼─────┐   │
    │                                  │        CONTENT SERVICE       │   │
    │                                  │                              │   │
    │                                  │• CMS Integration             │   │
    │                                  │• Content Delivery            │   │
    │                                  │• Personalization Rules       │   │
    │                                  └──────────────────────────────┘   │
    └─────────────────────────────────────────────────────────────────────┘

                                  DATA FLOW
┌───────────┐      ┌───────────┐      ┌───────────┐      ┌───────────┐
│   USER    │─────▶│  TRACKING │─────▶│   EVENT   │─────▶│  AI/ML    │
│ INTERACTION│     │  SERVICE  │     │ STREAMING │     │ PLATFORM  │
└───────────┘      └───────────┘      └────────────      └───────────┘
                                                              │
┌───────────┐      ┌───────────┐      ┌───────────┐          │
│ RESPONSE  │◀────│    API     │◀────│RECOMMENDATION◀───────┘
│ TO CLIENT │     │  GATEWAY   │     │  ENGINE    │
└───────────┘      └───────────┘      └───────────┘
                                 │              │
                     ┌───────────┴───────────┐  │
                     │                       │  │
               ┌─────▼─────┐           ┌─────▼─────┐  ┌─────────────┐
               │   USER    │           │  PRODUCT  │  │ CONTENT     │
               │ PROFILES  │           │ CATALOG   │  │ SERVICE     │
               │ DATABASE  │           │ DATABASE  │  │             │
               └───────────┘           └───────────┘  └──────┬──────┘
                                                              │
                                                      ┌──────▼──────┐
                                                      │ HEADLESS CMS│
                                                      │             │
                                                      │• Contentful │
                                                      │• Storyblok  │
                                                      │• Content    │
                                                      └─────────────┘

```
## Key Features with Technology Stack

**Recommendation Types & Technologies**

- Collaborative Filtering - "Customers like you also bought..."

  - ML Tools: Apache Spark MLlib, Amazon Personalize

  - Storage: Redis for user-item matrices, PostgreSQL

  - Processing: Real-time similarity scoring

- Content-Based - "Similar frames to your favorites"

  - ML Tools: Facebook FAISS, Google Vertex AI
  - Storage: Pinecone/Weaviate vector database
  - Processing: Cosine similarity, nearest neighbor search

- Real-Time - "Based on your recent browsing"

  - Streaming: Apache Kafka, AWS Kinesis, Apache Flink

  - Storage: Redis for session data, Apache Druid

  - Processing: Real-time feature computation

- Trending - "Popular items in your area"

  - Analytics: Apache Spark Streaming, Rockset

  - Storage: Elasticsearch, TimescaleDB

  - Processing: Geographic trending algorithms

- Scalability & Resilience Technologies
  - Auto-scaling
   - Kubernetes: HPA (Horizontal Pod Autoscaler)
   - Cloud: AWS Application Auto Scaling, Azure Autoscale
   - Metrics: Prometheus, Custom metrics adapter

- Circuit Breakers

  - Libraries: Resilience4j, Netflix Hystrix
  - Service Mesh: Istio, Linkerd
  - Implementation: Spring Boot, Envoy proxy

- Fallback Mechanisms
  - Caching: Redis cluster with failover
  - CDN: AWS CloudFront, Akamai static content
  - Strategy: Popular products cache, Static recommendations

- Multi-region Deployment
  - Orchestration: Kubernetes Federation
  - Networking: AWS Global Accelerator, Azure Traffic Manager
  - Database: AWS Aurora Global, Google Cloud Spanner

- Observability Technologies
  - Performance Monitoring
  - APM: Datadog, New Relic, Dynatrace
  - Tracing: Jaeger, Zipkin, AWS X-Ray
  - Metrics: Prometheus, Grafana dashboards

- Recommendation Quality
  - ML Monitoring: MLflow, Evidently AI
  - A/B Testing: Optimizely, Statsig, LaunchDarkly
  - Metrics: CTR tracking, Conversion attribution

- Business Impact Tracking

  - Analytics: Google Analytics 4, Adobe Analytics, Mixpanel
  - Data Pipeline: Segment, Snowplow Analytics
  - Dashboards: Tableau, Google Looker, Grafana

- Real-time Alerting
- Alert Management: PagerDuty, OpsGenie
- Monitoring: Prometheus Alertmanager, CloudWatch Alarms
- Notifications: Slack/MS Teams webhooks, Email/SMS

## Data Flow
User Interaction → Behavior Tracking Service captures events

Event Stream → Real-time processing and feature updates

Recommendation Request → API Gateway routes to appropriate services

Data Aggregation → User profile + product catalog + real-time context

ML Inference → Multiple algorithms generate candidate recommendations

Ranking & Filtering → Business rules and personalization applied

Response Delivery → Personalized suggestions to client applications


