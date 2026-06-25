2026-06-22 16:42

Status: #adult

Tags: [[x-road]]
- - -
# Security Server / [[X Road - Server de Segurança]]

Security Server is the entry point to X-Road, and it is required for both producing and consuming services via X-Road. The Security Server mediates service calls and service responses between Information Systems. The Security Server encapsulates the security aspects of the X-Road infrastructure: managing keys for signing and authentication, sending messages over a secure channel, creating the proof value for messages with digital signatures, time-stamping and logging. For a service consumer and a service provider Information System, the Security Server offers a REST-based and a SOAP-based message protocol. The protocol is the same for both the client and the service provider, making the Security Server transparent to the applications.

A single Security Server can host several organizations (multi-tenancy). The organization managing the Security Server is the server owner, and the hosted organizations are Security Server clients.

The Security Server manages two types of keys. The authentication keys are assigned to a Security Server and used for establishing cryptograpdhically secure communication channels with other Security Servers. The signing keys are assigned to the Security Server's clients and used for signing the exchanged messages. A trusted certification authority issues certificates for the keys. Certificates issued by other certification authorities are considered invalid. 

To be able to mediate messages, the Security Server must have a valid copy of the global configuration available all the time. The Security Server downloads the global configuration from the Central Server regularly and uses a local copy while processing messages. The Security Server remains operational as long as it has a valid copy of the global configuration available locally. Similarly, certificate validity information is downloaded from the Certificate Authority and cached locally. Caching allows the Security Server to operate even when the configuration data sources are unavailable.

The Security Server has an internal client-side load balancer, and it also supports external load balancing. The client-side load balancer is a built-in feature, and it provides high availability. Instead, external load balancing provides both high availability and scalability from a performance point of view.

- - -
## Security Server — Visão geral

```mermaid
flowchart LR
    A[Information System A<br>Service Consumer] -->|REST / SOAP| SSA[Security Server A]
    SSA -->|Secure X-Road Message| SSB[Security Server B]
    SSB -->|REST / SOAP| B[Information System B<br>Service Provider]
```

---

## Security Server — Funções

```mermaid
flowchart TD
    SS[Security Server]

    SS --> A[Entry point to X-Road]
    SS --> B[Mediates service calls]
    SS --> C[Mediates service responses]
    SS --> D[Authentication]
    SS --> E[Digital signatures]
    SS --> F[Secure channel]
    SS --> G[Time-stamping]
    SS --> H[Logging]
```

---

## Fluxo de chamada

```mermaid
sequenceDiagram
    participant C as Consumer Information System
    participant SSA as Security Server A
    participant SSB as Security Server B
    participant P as Provider Information System

    C->>SSA: REST / SOAP Request
    SSA->>SSA: Sign + Authenticate + Log
    SSA->>SSB: Secure X-Road Message
    SSB->>SSB: Validate + Timestamp + Log
    SSB->>P: REST / SOAP Request

    P-->>SSB: Service Response
    SSB-->>SSA: Secure X-Road Response
    SSA-->>C: REST / SOAP Response
```

---

## Multi-tenancy

```mermaid
flowchart TD
    SS[Security Server]

    SS --> Owner[Server Owner]
    SS --> OrgA[Client Organization A]
    SS --> OrgB[Client Organization B]
    SS --> OrgC[Client Organization C]
```

---

## Tipos de chaves

```mermaid
flowchart TD
    SS[Security Server]

    SS --> AK[Authentication Keys]
    SS --> SK[Signing Keys]

    AK --> AK1[Assigned to Security Server]
    AK --> AK2[Secure communication<br>between Security Servers]

    SK --> SK1[Assigned to Security Server Clients]
    SK --> SK2[Sign exchanged messages]

    AK --> CA[Trusted Certification Authority]
    SK --> CA
```

---

## Configuração global

```mermaid
flowchart TD
    CS[Central Server] -->|Global Configuration| SS[Security Server]
    CA[Certification Authority] -->|Certificate Validity Info| SS

    SS --> LC[Local Cached Configuration]
    SS --> CC[Cached Certificate Info]

    LC --> MP[Message Processing]
    CC --> MP
```

---

## Cache local

```mermaid
flowchart TD
    SS[Security Server]

    SS --> GC[Cached Global Configuration]
    SS --> CV[Cached Certificate Validity Info]

    GC --> OP[Operational Security Server]
    CV --> OP

    OP --> MSG[Mediates Messages]
```

---

## Load balancing

```mermaid
flowchart TD
    SS[Security Server]

    SS --> ILB[Internal Client-side Load Balancer]
    SS --> ELB[External Load Balancing]

    ILB --> HA1[High Availability]
    ELB --> HA2[High Availability]
    ELB --> SC[Scalability]
    ELB --> PF[Performance]
```

---

## Resumo final

```mermaid
flowchart LR
    A[Consumer System] -->|REST / SOAP| B[Security Server]
    B -->|Secure Signed Message| C[Other Security Server]
    C -->|REST / SOAP| D[Provider System]
```
- - -
# Referências
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23560180-architecture