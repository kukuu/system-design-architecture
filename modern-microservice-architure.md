# Personalised Product Recommendations Architecture

## MACH-based Cloud Native Solution for Specsavers

**MACH = Microservices + API-first + Cloud-native + Headless**
  - Microservices: Independent, single-purpose services

  - API-first: All functionality exposed via APIs
  - Cloud-native: SaaS, scalable, resilient
  - Headless: Frontend decoupled from backend

**Benefits for Specsavers:**

 - Faster feature development
 - Better scalability during promotions
 - Flexible omnichannel experiences
 - Future-proof technology stack

**Key PILLARS of any Modern Architecture**

- Scalability: Independent scaling of microservices ensures that services can handle their unique workloads without impacting others.
- Speed: Optimized data flows and caching strategies enable real-time recommendations and fast response times.
- Resilience: Asynchronous communication and circuit breakers mitigate the risk of cascading failures.
- Flexibility: A modular approach allows for iterative development, making introducing new features or updating existing ones easier.
- Observability: Centralized logging and monitoring enable debugging and performance analysis for distributed services.
- Security: API Gateway and token-based authentication (OAuth2) ensure a secure entry point.


**Architecture**

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
    │                                                            │         │
    │  ┌─────────────┐                                  ┌───────▼───────┐  │
    │  │   AI/ML     │                                  │  FEATURE      │  │
    │  │  PLATFORM   │                                  │   STORE       │  │
    │  │             │                                  │               │  │
    │  │• Training   │                                  │• Real-time    │  │
    │  │• Inference  │                                  │  Features     │  │
    │  │• Monitoring │                                  │• Batch        │  │
    │  └─────────────┘                                  │  Features     │  │
    │                                                   └───────────────┘  │
    └──────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────────┐
│                         INFRASTRUCTURE & OBSERVABILITY                      │
│                                                                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │ KUBERNETES  │  │   SERVICE    │  │  MONITORING │  │    CI/CD    │        │
│  │  CLUSTER    │  │   MESH      │  │   STACK     │  │  PIPELINE   │        │
│  │             │  │             │  │             │  │             │        │
│  │• Auto-scale │  │• Istio      │  │• Prometheus │  │• Jenkins    │        │
│  │• Container  │  │• Circuit    │  │• Grafana    │  │• GitOps     │        │
│  │• Orchestration│ │ Breakers   │  │• Jaeger     │  │• Automated  │        │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────────────────────┘

                                  DATA FLOW
┌───────────┐      ┌───────────┐      ┌───────────┐      ┌───────────┐
│   USER    │─────▶│  TRACKING │─────▶│   EVENT   │─────▶│  AI/ML    │
│ INTERACTION│     │  SERVICE  │     │ STREAMING │     │ PLATFORM  │
└───────────┘      └───────────┘      └───────────┘      └───────────┘
                                                              │
┌───────────┐      ┌───────────┐      ┌───────────┐          │
│ RESPONSE  │◀────│    API     │◀────│RECOMMENDATION◀───────┘
│ TO CLIENT │     │  GATEWAY   │     │  ENGINE    │
└───────────┘      └───────────┘      └───────────┘
                                 │
                     ┌───────────┴───────────┐
                     │                       │
               ┌─────▼─────┐           ┌─────▼─────┐
               │   USER    │           │  PRODUCT  │
               │ PROFILES  │           │ CATALOG   │
               │ DATABASE  │           │ DATABASE  │
               └───────────┘           └───────────┘
```
