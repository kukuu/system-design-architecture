# Leveraging SPARX EA and LeanIX for StrikeNet and Repair Smart 

## StrikeNet (UK MoD Digital Twin) 
- SPARX EA was in use to architect the combat system virtualization using SysML to simulate AI-driven scenarios e.g., threat prediction, with LeanIX auditing AWS/AZURE costs and Cyber Essentials compliance. 

 - MoD Stakeholders strategists reviewed SPARX EA’s simulation traces against LeanIX risk registers, ensuring AI recommendations align with "cost-efficient resilience."

## Repair Smart (BAE, AirBus, SPIRIT)

- For Repair Smart, SPARX EA’s Activity Diagrams was used to validate AI search algorithms against repair SLAs, while LeanIX was used for benchmarking time savings against industry standards (e.g., MRO).


```
┌───────────────────────┐    ┌───────────────────────┐    ┌───────────────────────┐
│   STAKEHOLDERS        │    │      SPARX EA         │    │       LeanIX          │
│  - MoD (Risk/Cost)    │◄──►│ - ArchiMate/SysML     │◄──►│ - Fact Sheets         │
│  - Aerospace (SLAs)   │    │ - BPMN/Simulations    │    │ - Risk Heatmaps       │
└───────────┬───────────┘    └───────────┬───────────┘    └───────────┬───────────┘
            │                            │                            │
            ▼                            ▼                            ▼
┌───────────────────────────────────────────────────────────────────────────────┐
│                          TECHNICAL EXECUTION                                  │
│  Repair Smart: AI Templates → Kafka → AWS Lambda                              │
│  StrikeNet: IoT Sensors → Digital Twin → Kubernetes/AI → MoD Dashboards       │
└───────────────────────────────────────────────────────────────────────────────┘
```


## more.....
https://github.com/kukuu/system-design-architecture/blob/master/sparxEA-lenIX-in-defence-projs.md
