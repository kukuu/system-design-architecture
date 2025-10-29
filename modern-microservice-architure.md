# Personalised Product Recommendations Architecture

## MACH-based Cloud Native Solution for Specsavers

**Microservices + API-first + Cloud-native + Headless**
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

- **Scalability**: Independent scaling of microservices ensures that services can handle their unique workloads without impacting others.
- **Speed**: Optimized data flows and caching strategies enable real-time recommendations and fast response times.
- **Resilience**: Asynchronous communication and circuit breakers mitigate the risk of cascading failures.
- **Flexibility**: A modular approach allows for iterative development, making introducing new features or updating existing ones easier.
- **Observability**: Centralized logging and monitoring enable debugging and performance analysis for distributed services.
- **Security**: API Gateway and token-based authentication (OAuth2) ensure a secure entry point.
   
## High Level Architecture

```
┌─────────────────┐
│   MOBILE CLIENT │
└─────────────────┘
         ├───────────────────────┐
         │                       │
    (Access Token)         (Sends JWT)
         │                       │
         ▼                       ▼
┌──────────────────┐ JWT ┌──────────────────┐
│ AUTHORIZATION    │◄────│   API GATEWAY    │
│ SERVER           │────►│                  │
└──────────────────┘     └──────────────────┘
         │                      │
         │ (Access Token)       │ (JWT)
         ▼                      ▼
┌──────────────────┐    ┌──────────────────┐
│   USER STORE     │    │    REST API      │
│                  │    │                  │
└──────────────────┘    └──────────────────┘
                                │
                                │ 
                                ▼
                    ┌─────────────────────────┐
                    │ USER PROFILE SERVICE    │
                    ├─────────────────────────┤
                    │ PRODUCT CATALOG SERVICE │
                    ├─────────────────────────┤
                    │ RECOMMENDATION ENGINE   │
                    │ SERVICE                 │
                    ├─────────────────────────┤
                    │ BEHAVIOR TRACKING       │
                    │ SERVICE                 │
                    └─────────────────────────┘



```

## Low LevelArchitecture

### Core Components

- Microservices Layer

  - User Profile Service: Customer preferences and history

  - Product Catalog Service: Glasses, lenses, and accessories data

  - Recommendation Engine: Generates personalized suggestions

  - Behavior Tracking Service: Captures browsing and purchase events

- Content Service: Manages promotional content and assets

### Service Communication 
Services communicate via **REST APIs for synchronous requests** and **Apache Kafka for asynchronous event streaming**. The **API Gateway (GraphQL/REST) routes client requests**, while **services** exchange data through defined **contracts**.


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
       └────────────────┼────────────────┼────────────────┘
                        │                │
                 ┌──────▼────────────────▼──────┐
                 │         API GATEWAY          │                                                     
                 │  ┌────────────────────────┐  │
                 │  │ GraphQL • REST • Auth  │  │
                 │  │ Rate Limiting • Caching│  │
                 │  └────────────────────────┘  │
                 └───────────────┬──────────────┘
                                 │
    ┌────────────────────────────┼─────────────────────────────────────────┐
    │                      MICROSERVICES LAYER                             │
    │                                                                      │
    │  ┌─────────────┐  ┌─────────────┐  ┌─────────────-┐  ┌─────────────┐ │
    │  │   USER      │  │  PRODUCT    │  │RECOMMENDATION│  │ BEHAVIOR    │ │
    │  │  PROFILE    │  │  CATALOG    │  │   ENGINE     │  │ TRACKING    │ │
    │  │ SERVICE     │  │ SERVICE     │  │              │  │ SERVICE     │ │
    │  └──────┬──────┘  └──────┬──────┘  └──────┬──────-┘  └─────┬──────-┘ │
    │         │                │                │                │         │
    └─────────┼────────────────┼────────────────┼────────────────┼────────-┘
              │                │                │                │
    ┌─────────▼────────────────▼────────────────▼────────────────▼─────────┐
    │                        DATA & AI/ML LAYER                            │
    │                                                                      │
    │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  │
    │  │  USER       │  │  PRODUCT    │  │   VECTOR    │  │  EVENT      │  │
    │  │ PROFILES    │  │ CATALOG DB  │  │  DATABASE   │  │ STREAMING   │  │
    │  │  DATABASE   │  │             │  │             │  │   (Kafka)   │  │
    │  └─────────────┘  └─────────────┘  └─────────────┘  └──────┬──────┘  │
    │                                                            │         │
    │  ┌─────────────┐                                    ┌──────▼──────┐  │
    │  │   AI/ML     │                                    │  CONTENT    │  │
    │  │  PLATFORM   │                                    │  SERVICE    │  │
    │  └──────┬──────┘                                    └──────┬──────┘  │
    │         │                                                  │         │
    └─────────┼──────────────────────────────────────────────────┼───────--┘
              │                                                  │
    ┌─────────▼──────┐                                  ┌────────▼────────┐
    │  HEADLESS CMS  │                                  │   FEATURE       │
    │                │                                  │     STORE       │
    │ • Contentful   │                                  │                 │
    │ • Storyblok    │                                  │ • Real-time     │
    │ • Content      │                                  │   Features      │
    │   Fragments    │                                  │ • Batch         │
    │ • Promotions   │                                  │   Features      │
    └────────────────┘                                  └─────────────────┘
```




### Data Layer

- Event Streaming: Real-time user behavior tracking

- Product Database: Complete catalog with attributes

- User Profiles: Customer preferences and history

- Vector Database: Similarity search for recommendations


## Data Flow
                     

                          ┌─────────────────┐
                          │   USER          │
                          │  INTERACTION    │
                          └────────┬────────┘
                                   │
              ┌────────────────────┼────────────────────┐
              │                    │                    │
         ┌────▼────┐         ┌─────▼─────┐        ┌─────▼─────┐
         │BEHAVIOR │         │   API     │        │ CONTENT   │
         │TRACKING │         │ GATEWAY   │        │ SERVICE   │
         └────┬────┘         └─────┬─────┘        └─────┬─────┘
              │                    │                    │
         ┌────▼────┐         ┌─────▼─────┐        ┌─────▼─────┐
         │ EVENT   │         │   USER    │        │ HEADLESS  │
         │STREAMING│         │  PROFILE  │        │   CMS     │
         └────┬────┘         └─────┬─────┘        └───────────┘
              │                    │                    │
         ┌────▼────┐         ┌─────▼─────┐              │
         │ AI/ML   │         │  PRODUCT  │              │
         │PLATFORM │         │  CATALOG  │              │
         └────┬────┘         └─────┬─────┘              │
              │                    │                    │
              └────────┐     ┌─────┘                    │
                       │     │                          │
                  ┌────▼─────▼─────┐                    │
                  │ RECOMMENDATION │◄───────────────────┘
                  │   ENGINE       │
                  └───────┬────────┘
                          │
                  ┌───────▼────────┐
                  │     API        │
                  │   GATEWAY      │
                  └───────┬────────┘
                          │
                  ┌───────▼────────┐
                  │    RESPONSE    │
                  │    TO USER     │
                  └────────────────┘


Real-time tracking events flow through Kafka to the AI platform, which updates recommendation models. Content Service syncs with Headless CMS via webhooks, and all services leverage Redis for caching to reduce latency.

#### Deailed Breakdown Data Layer

https://github.com/kukuu/system-design-architecture/blob/master/detailed-breakdown-data-layer-microservice.md

### AI/ML Layer

- Real-time Inference: Immediate recommendation generation

- Model Training: Batch processing for algorithm improvement

- Feature Store: Consolidated data for ML models

### The Synergy of AI, Content, and Services 

**1. AI/ML Platform: The "Brain" for Personalization**
   
The AI/ML Platform is the **intelligent core** that processes data to generate **insights**  and **predictions**. Its primary role is to move the system from being **reactive** ("show product X") to being **proactive** ("we recommend product X for you").

_How it correlates with other components:_

- Inputs (Data for Learning):

  - From User Profile DB & Behavior Tracking: It learns about individual user preferences, purchase history, and demographics.

  - From Event Streaming (Kafka): It consumes real-time user interactions (clicks, views, time on page, searches) to understand current intent and behavior patterns.

  - From Product Catalog DB: It understands product attributes, categories, and relationships.

- Outputs (Intelligence & Actions):

  - To Recommendation Engine: This is the most direct correlation. The AI/ML platform trains models (e.g., collaborative filtering, content-based filtering) that power the Recommendation Engine microservice. The engine uses these models to serve real-time, personalized product suggestions.

  - To Vector Database: It transforms unstructured data (like product descriptions, user reviews, or content from the CMS) into numerical representations (vectors). This enables powerful semantic search (e.g., "find me durable glasses for running") and similarity matching.



**2. Headless CMS: The "Centralized Content Hub"**

A Headless CMS (like Contentful or Storyblok) is decoupled from the presentation layer (web, mobile apps). It's purely a backend system for managing and structuring content.

_Its role in this architecture:_

- Manage Marketing & Editorial Content: This includes promotional banners, blog articles, help guides, product FAQs, and marketing copy.

- Structured Content Fragments: It doesn't just manage blobs of text. It can define structured content like "Promotion" (with fields for headline, description, image, validUntil, targetAudience).

- Content Agility: It allows marketers and content editors to create and update content without needing a full software deployment.

**3. Content Service: The "Content Delivery Bridge"**

The Content Service is a crucial microservice  sits between the Headless CMS and the rest of the application.

- Its role is to:
  - Fetch Content: Pull content from the Headless CMS via its APIs.
  - Transform & Structure: Process the raw content from the CMS into a format that the front-end applications expect.
  - Serve Content: Expose this content internally, likely through the API Gateway, so that the Web App, Mobile App, etc., can consume it.
 
**4. Putting it all together: The Powerful Synergy**

This is  a sophisticated data flow that enables hyper-personalization.

- Content is Created in Headless CMS: A marketer creates a new promotional campaign for "Blue Light Filtering Lenses" in Contentful, targeting students.

- Content is Served via Content Service: The Content Service fetches this "Blue Light Lenses" promotion and makes it available to the API Gateway.

- AI/ML Informs the Personalization Logic: Separately, the AI/ML platform has identified that a specific user (e.g., a university student who spends long hours on a computer) is a high-potential candidate for this promotion. This intelligence is embedded in the Recommendation Engine.

- The Orchestrated Response: When that specific user logs into the Web App:

  - The app makes a call for its homepage data through the API Gateway.

  - The gateway likely calls the Recommendation Engine to get personalized product suggestions.

  - The Recommendation Engine, using the AI/ML model, returns a list of recommended products, and it can also return a flag: "showBlueLightPromo": true.

  - Simultaneously, the gateway calls the Content Service to get the available promotions.

  - The front-end application now has both pieces: the content (promo banner) and the intelligence (show it to this user). It assembles them and displays the highly relevant "Blue Light Filtering Lenses" promotion to the student.

- Another crucial correlation: Enriching AI with Content Data

  - The structured content from the Headless CMS (e.g., product descriptions, article tags) can be fed into the AI/ML Platform.

  - The AI/ML platform can convert this text into vectors and store it in the Vector Database.
  - This allows the Recommendation Engine to perform semantic searches. For example, if a user reads a blog post about "sustainable products," the engine can find and recommend eyewear made from recycled materials because it understands the semantic link between the content and the products.

## Proposed Recommendation Types & Technologies

https://github.com/kukuu/system-design-architecture/blob/master/recommendations-proposed-types-and-technologies.md

## Security & Governance:

OAuth2/JWT authentication secures API access, while TLS 1.3 encrypts data in transit. Role-based access control (RBAC) governs data permissions, and GDPR compliance ensures customer data protection. Centralized monitoring (Prometheus/Grafana) and API gateway rate limiting maintain system governance and prevent abuse.
   
## Benefits for Specsavers:

- Marketing teams can quickly update promotional banners for frames, lens offers, or seasonal campaigns

- Consistent content across all channels (web, mobile, in-store)

- A/B testing of promotional content alongside product recommendations

- Separation of content management from application logic

## Challenges & Mitigation:

Key challenges include data consistency across distributed services, mitigated through event sourcing and eventual consistency; service orchestration complexity, addressed with Kubernetes and service mesh; and potential latency in real-time recommendations, solved with Redis caching and API gateway optimization.



## Conclusion:

 The AI/ML platform consumes user, product, and behavioral data to create models that enable personalized recommendations and intelligent search.

The architecture ensures  a personalised product recommendations architecture and data management while maintaining data integrity across all microservices for real-time personalized recommendations.
