2026-06-22 16:44

Status: #adult 

Tags: [[x-road]]
- - -
# Information System / [[X Road - Sistema de Informação]]

The Information System produces and/or consumes services via X-Road and is owned by an X-Road member. X-Road supports consuming and producing both REST and SOAP services. However, X-Road does not provide automatic conversions between different types of messages and services.

For a service consumer Information System, the Security Server acts as an entry point to all the X-Road services. The consumer can discover registered X-Road members and their available services by using the X-Road metadata protocol.

A service provider Information System implements a REST and/or SOAP service and makes it available over the X-Road. Existing REST services do not require any changes – they can be published as-is. Instead, SOAP services must implement the X-Road message protocol for SOAP. Service descriptions of REST services are defined using OpenAPI3 specification, and service descriptions of SOAP services are defined using WSDL. Service consumers can download service descriptions using the X-Road metadata protocol.

- - -
## Information System — Visão geral

```mermaid
flowchart TD
    M[X-Road Member] --> IS[Information System]

    IS --> P[Produces Services]
    IS --> C[Consumes Services]

    IS --> REST[REST Services]
    IS --> SOAP[SOAP Services]

    REST --> XR[X-Road]
    SOAP --> XR[X-Road]
```

---

## Produção e consumo de serviços

```mermaid
flowchart LR
    Consumer[Consumer Information System] -->|Consumes Service| SS1[Security Server]
    SS1 -->|X-Road| SS2[Security Server]
    SS2 -->|Provides Access| Provider[Provider Information System]

    Provider -->|Produces REST / SOAP Service| SS2
```

---

## X-Road não converte mensagens automaticamente

```mermaid
flowchart TD
    XR[X-Road]

    REST[REST Message] --> XR
    SOAP[SOAP Message] --> XR

    XR --> REST2[REST Message]
    XR --> SOAP2[SOAP Message]

    REST -. No automatic conversion .- SOAP
```

---

## Consumer Information System

```mermaid
flowchart TD
    CIS[Consumer Information System]

    CIS --> SS[Security Server]
    SS --> XR[X-Road Services]

    CIS --> META[X-Road Metadata Protocol]
    META --> MEMBERS[Registered X-Road Members]
    META --> SERVICES[Available Services]
```

---

## Descoberta de serviços

```mermaid
sequenceDiagram
    participant Consumer as Consumer Information System
    participant SS as Security Server
    participant Meta as X-Road Metadata Protocol
    participant Registry as X-Road Members and Services

    Consumer->>SS: Request available services
    SS->>Meta: Use metadata protocol
    Meta->>Registry: Discover members and services
    Registry-->>Meta: Return registered services
    Meta-->>SS: Service metadata
    SS-->>Consumer: Available services
```

---

## Provider Information System

```mermaid
flowchart TD
    PIS[Provider Information System]

    PIS --> REST[Implements REST Service]
    PIS --> SOAP[Implements SOAP Service]

    REST --> PUB[Published over X-Road]
    SOAP --> PROTOCOL[Must implement X-Road SOAP Message Protocol]
    PROTOCOL --> PUB

    PUB --> SS[Security Server]
    SS --> XR[X-Road]
```

---

## REST Services

```mermaid
flowchart TD
    REST[Existing REST Service]

    REST --> NOCHANGE[No changes required]
    NOCHANGE --> PUBLISH[Can be published as-is]
    PUBLISH --> XR[X-Road]
```

---

## SOAP Services

```mermaid
flowchart TD
    SOAP[SOAP Service]

    SOAP --> REQUIRED[Must implement X-Road SOAP Message Protocol]
    REQUIRED --> PUBLISH[Published over X-Road]
    PUBLISH --> XR[X-Road]
```

---

## Service descriptions

```mermaid
flowchart TD
    SERVICE[Service Description]

    SERVICE --> REST[REST Service]
    SERVICE --> SOAP[SOAP Service]

    REST --> OPENAPI[OpenAPI 3 Specification]
    SOAP --> WSDL[WSDL]

    OPENAPI --> META[X-Road Metadata Protocol]
    WSDL --> META

    META --> CONSUMER[Service Consumer Downloads Description]
```

---

## Fluxo completo

```mermaid
flowchart LR
    MemberA[X-Road Member A] --> ConsumerIS[Consumer Information System]
    MemberB[X-Road Member B] --> ProviderIS[Provider Information System]

    ConsumerIS -->|REST / SOAP| SSA[Security Server A]
    SSA -->|Metadata Protocol| Discovery[Discover Members and Services]

    SSA -->|X-Road Message| SSB[Security Server B]
    SSB -->|REST / SOAP| ProviderIS

    ProviderIS --> REST[REST Service<br>OpenAPI 3]
    ProviderIS --> SOAP[SOAP Service<br>WSDL]
```

---

## Resumo visual

```mermaid
flowchart LR
    A[Information System] --> B[Security Server]
    B --> C[X-Road]
    C --> D[Other Security Server]
    D --> E[Provider Information System]

    E --> F[REST Service]
    E --> G[SOAP Service]

    F --> H[OpenAPI 3]
    G --> I[WSDL]
```

- - -
# Referências
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23560180-architecture