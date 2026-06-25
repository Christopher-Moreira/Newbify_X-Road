2026-06-22 16:54

Status: #adult 

Tags: [[x-road]]
- - -
# Time-Stamping Authority (TSA) / [[X Road - Autoridade de Registro de Tempo (TSA)]]

All the messages sent via X-Road are time-stamped and logged by the Security Server. The purpose of the time-stamping is to certify the existence of data items at a certain point in time. The TSA provides a time-stamping service that the Security Server uses for time-stamping all the incoming/outgoing requests/responses. Only trusted TSAs that are defined in the Central Server can be used.

The time-stamping authority must implement the time-stamping protocol supported by X-Road. X-Road uses batch time-stamping, which reduces the load of the time-stamping service. The load does not depend on the number of messages exchanged over the X-Road. Instead, it depends on the number of Security Servers in the system.

- - -
## Time-Stamping Authority — Visão geral

```mermaid
flowchart TD
    TSA[Time-Stamping Authority<br>TSA]

    TSA --> TS[Provides Time-Stamping Service]
    TS --> SS[Security Server]

    SS --> IN[Incoming Requests]
    SS --> OUT[Outgoing Requests]
    SS --> RESP[Responses]

    IN --> LOG[Time-stamped and Logged]
    OUT --> LOG
    RESP --> LOG
```

---

## Papel da TSA no X-Road

```mermaid
flowchart LR
    A[Information System A] --> SSA[Security Server A]
    SSA -->|Request / Response| SSB[Security Server B]
    SSB --> B[Information System B]

    SSA -->|Time-stamping| TSA[Trusted TSA]
    SSB -->|Time-stamping| TSA

    SSA --> LOGA[Message Log]
    SSB --> LOGB[Message Log]
```

---

## Certificação da existência dos dados

```mermaid
flowchart TD
    MSG[Message / Data Item]

    MSG --> SS[Security Server]
    SS --> TSA[Time-Stamping Authority]

    TSA --> STAMP[Timestamp Token]
    STAMP --> PROOF[Proof that data existed<br>at a certain point in time]

    PROOF --> LOG[Logged by Security Server]
```

---

## Apenas TSAs confiáveis

```mermaid
flowchart TD
    CS[Central Server]

    CS --> TRUSTED[Trusted TSAs List]

    TRUSTED --> TSA1[Trusted TSA A]
    TRUSTED --> TSA2[Trusted TSA B]

    TSA3[Untrusted TSA] -. Not allowed .-> SS[Security Server]

    TSA1 --> SS
    TSA2 --> SS
```

---

## TSA definida no Central Server

```mermaid
flowchart LR
    CS[Central Server] -->|Defines trusted TSAs| GC[Global Configuration]
    GC -->|Downloaded by| SS[Security Server]
    SS -->|Uses only trusted TSA| TSA[Time-Stamping Authority]
```

---

## Fluxo de time-stamping

```mermaid
sequenceDiagram
    participant IS as Information System
    participant SS as Security Server
    participant TSA as Trusted TSA
    participant LOG as Message Log

    IS->>SS: Send request / response
    SS->>SS: Prepare message proof
    SS->>TSA: Request timestamp
    TSA-->>SS: Timestamp token
    SS->>LOG: Store timestamped message log
```

---

## Time-stamping em mensagens de entrada e saída

```mermaid
flowchart TD
    SS[Security Server]

    IN[Incoming Request] --> SS
    OUT[Outgoing Request] --> SS
    RESIN[Incoming Response] --> SS
    RESOUT[Outgoing Response] --> SS

    SS --> TSA[Trusted TSA]
    TSA --> TS[Timestamp]

    TS --> LOG[Security Server Log]
```

---

## Batch time-stamping

```mermaid
flowchart TD
    SS[Security Server]

    M1[Message 1] --> BATCH[Batch of Message Proofs]
    M2[Message 2] --> BATCH
    M3[Message 3] --> BATCH
    M4[Message 4] --> BATCH

    BATCH --> TSA[Time-Stamping Authority]
    TSA --> TOKEN[Batch Timestamp Token]

    TOKEN --> LOG[Logged Timestamped Batch]
```

---

## Redução de carga com batch time-stamping

```mermaid
flowchart TD
    MANY[Many X-Road Messages]

    MANY --> SS1[Security Server A]
    MANY --> SS2[Security Server B]
    MANY --> SS3[Security Server C]

    SS1 --> B1[Batch Timestamp Request]
    SS2 --> B2[Batch Timestamp Request]
    SS3 --> B3[Batch Timestamp Request]

    B1 --> TSA[Time-Stamping Authority]
    B2 --> TSA
    B3 --> TSA
```

---

## Carga depende dos Security Servers

```mermaid
flowchart TD
    LOAD[TSA Load]

    LOAD --> NOTMSG[Does not depend directly<br>on number of messages]
    LOAD --> SERVERS[Depends on number of<br>Security Servers]

    SERVERS --> SS1[Security Server A]
    SERVERS --> SS2[Security Server B]
    SERVERS --> SS3[Security Server C]
```

---

## Resumo visual

```mermaid
flowchart LR
    MSG[X-Road Message] --> SS[Security Server]
    SS --> TSA[Trusted TSA]
    TSA --> TS[Timestamp]
    TS --> LOG[Message Log]
    LOG --> PROOF[Proof of existence<br>at a specific time]
```

- - -
# Referências
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23560180-architecture