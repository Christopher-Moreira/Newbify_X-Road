2026-06-22 16:37

Status: #adult 

Tags: [[x-road]]
- - -
# Central Services /  [[X Road - Serviços Centrais]]

Central services consist of Central Server and Configuration Proxy. Central Server contains the registry of X-Road members and their Security Servers. Besides, the Central Server contains the security policy of the X-Road instance that includes a list of trusted certification authorities, a list of trusted time-stamping authorities, and configuration parameters. Both the member registry and the security policy are made available to the Security Servers via HTTP and HTTPS protocols. This distributed set of data forms the global configuration that Security Servers use for mediating the messages sent via X-Road. The X-Road Operator is responsible for operating the Central Server. 

Configuration Proxy is an optional component that can be used as a proxy for publishing the global configuration to Security Servers for download. The Configuration Proxy first downloads the global configuration from the Central Server and then further distributes it securely. The Configuration Proxy can be used to increase system availability by creating an additional configuration source and reduce the load on the Central Server. The X-Road Operator is responsible for operating the Configuration Proxy.

- - - 
## Central Services — Visão geral

```mermaid
flowchart TD
    OP[X-Road Operator]

    OP --> CS[Central Server]
    OP --> CP[Configuration Proxy<br>Optional]

    CS --> MR[Member Registry]
    CS --> SP[Security Policy]

    MR --> M[X-Road Members]
    MR --> SSREG[Registered Security Servers]

    SP --> CA[Trusted Certification Authorities]
    SP --> TSA[Trusted Time-Stamping Authorities]
    SP --> PARAM[Configuration Parameters]
```

---

## Central Server distribuindo configuração global

```mermaid
flowchart TD
    CS[Central Server]

    CS --> GC[Global Configuration]

    GC --> MR[Member Registry]
    GC --> SP[Security Policy]
    GC --> CA[Trusted CAs]
    GC --> TSA[Trusted TSAs]
    GC --> PARAM[Configuration Parameters]

    GC -->|HTTP / HTTPS| SS1[Security Server A]
    GC -->|HTTP / HTTPS| SS2[Security Server B]
    GC -->|HTTP / HTTPS| SS3[Security Server C]

    SS1 --> MSG1[Mediates X-Road Messages]
    SS2 --> MSG2[Mediates X-Road Messages]
    SS3 --> MSG3[Mediates X-Road Messages]
```

---

## Security Servers usando a configuração global

```mermaid
flowchart LR
    CS[Central Server] -->|Global Configuration<br>HTTP / HTTPS| SSA[Security Server A]
    CS -->|Global Configuration<br>HTTP / HTTPS| SSB[Security Server B]

    SSA -->|Uses configuration| A[Message Mediation]
    SSB -->|Uses configuration| B[Message Mediation]

    A --> X[X-Road Data Exchange]
    B --> X
```

---

## Central Server com Configuration Proxy

```mermaid
flowchart TD
    CS[Central Server] -->|Downloads Global Configuration| CP[Configuration Proxy<br>Optional]

    CP -->|Securely Redistributes Configuration| SS1[Security Server A]
    CP -->|Securely Redistributes Configuration| SS2[Security Server B]
    CP -->|Securely Redistributes Configuration| SS3[Security Server C]

    SS1 --> X[X-Road Message Exchange]
    SS2 --> X
    SS3 --> X
```

---

## Sem Configuration Proxy

```mermaid
flowchart TD
    CS[Central Server]

    CS -->|HTTP / HTTPS| SS1[Security Server A]
    CS -->|HTTP / HTTPS| SS2[Security Server B]
    CS -->|HTTP / HTTPS| SS3[Security Server C]
```

---

## Com Configuration Proxy

```mermaid
flowchart TD
    CS[Central Server] --> CP[Configuration Proxy]

    CP --> SS1[Security Server A]
    CP --> SS2[Security Server B]
    CP --> SS3[Security Server C]
```

---

## Função do Configuration Proxy

```mermaid
flowchart TD
    CP[Configuration Proxy]

    CP --> A[Downloads Global Configuration<br>from Central Server]
    CP --> B[Publishes Configuration<br>to Security Servers]
    CP --> C[Increases Availability]
    CP --> D[Reduces Load<br>on Central Server]
    CP --> E[Additional Configuration Source]
```

---

## Fluxo completo

```mermaid
flowchart TD
    OP[X-Road Operator]

    OP --> CS[Central Server]
    OP --> CP[Configuration Proxy<br>Optional]

    CS --> GC[Global Configuration]

    GC --> MR[Member Registry]
    GC --> SP[Security Policy]

    MR --> MEMBERS[X-Road Members]
    MR --> SERVERS[Security Servers Registry]

    SP --> CA[Trusted CAs]
    SP --> TSA[Trusted TSAs]
    SP --> PARAMS[Configuration Parameters]

    CS -->|HTTP / HTTPS| SS1[Security Server A]
    CS -->|HTTP / HTTPS| SS2[Security Server B]

    CS -->|Downloads Global Configuration| CP
    CP -->|Secure Distribution| SS3[Security Server C]
    CP -->|Secure Distribution| SS4[Security Server D]

    SS1 --> X[X-Road Secure Data Exchange]
    SS2 --> X
    SS3 --> X
    SS4 --> X
```
O **Central Server** é a fonte principal da configuração global.  
O **Configuration Proxy** é opcional e serve para aumentar disponibilidade e reduzir carga no Central Server.
- - -
# Referências
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23560180-architecture