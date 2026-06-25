2026-06-22 17:00

Status: #adult 

Tags: [[x-road]]
- - -
# Certification Authority (CA) / [[X Road - Autoridade de Certificação (AC)]]

The certification authority (CA) issues certificates to Security Servers (authentication certificates) and X-Road member organizations (signing certificates). Authentication certificates are used for securing the connection between two Security Servers. Signing certificates are used for digitally signing the messages sent by X-Road members. Only certificates issued by trusted certification authorities that are defined in the Central Server can be used. 

The Security Server checks the validity of the signing and authentication certificates via the Online Certificate Status Protocol (OCSP). Each Security Server is responsible for querying the validity information of its certificates and then sharing the information with other Security Servers as a part of the message exchange process. Only Security Servers with valid signing and authentication certificates can exchange messages with other Security Servers.

- - -

## Certification Authority — Visão geral

```mermaid
flowchart TD
    CA[Certification Authority<br>CA]

    CA --> AUTH[Authentication Certificates]
    CA --> SIGN[Signing Certificates]

    AUTH --> SS[Security Servers]
    SIGN --> MEMBERS[X-Road Member Organizations]
```

---

## Tipos de certificados

```mermaid
flowchart TD
    CA[Certification Authority]

    CA --> AC[Authentication Certificate]
    CA --> SC[Signing Certificate]

    AC --> AC1[Issued to Security Server]
    AC --> AC2[Secures connection between<br>two Security Servers]

    SC --> SC1[Issued to X-Road Member]
    SC --> SC2[Digitally signs messages<br>sent by X-Road members]
```

---

## Certificados confiáveis definidos no Central Server

```mermaid
flowchart TD
    CS[Central Server]

    CS --> TRUSTED[Trusted Certification Authorities]

    TRUSTED --> CA1[Trusted CA A]
    TRUSTED --> CA2[Trusted CA B]

    CA3[Untrusted CA] -. Certificates invalid .-> SS[Security Server]

    CA1 --> CERT1[Valid Certificates]
    CA2 --> CERT2[Valid Certificates]

    CERT1 --> SS
    CERT2 --> SS
```

---

## Certificado de autenticação entre Security Servers

```mermaid
flowchart LR
    SSA[Security Server A] -->|Authentication Certificate| SECURE[Secure Connection]
    SSB[Security Server B] -->|Authentication Certificate| SECURE

    SECURE --> EXCHANGE[X-Road Message Exchange]
```

---

## Certificado de assinatura de mensagens

```mermaid
flowchart LR
    MEMBER[X-Road Member] -->|Signing Certificate| MSG[Digitally Signed Message]
    MSG --> SS[Security Server]
    SS --> XR[X-Road]
```

---

## Validação de certificados via OCSP

```mermaid
flowchart TD
    SS[Security Server]

    SS --> CERT[Signing / Authentication Certificate]
    CERT --> OCSP[OCSP<br>Online Certificate Status Protocol]

    OCSP --> VALID[Certificate Valid]
    OCSP --> INVALID[Certificate Invalid]

    VALID --> ALLOW[Allow Message Exchange]
    INVALID --> BLOCK[Block Message Exchange]
```

---

## Security Server consultando validade dos certificados

```mermaid
sequenceDiagram
    participant SS as Security Server
    participant OCSP as OCSP Service
    participant CA as Certification Authority

    SS->>OCSP: Query certificate validity
    OCSP->>CA: Check certificate status
    CA-->>OCSP: Certificate status
    OCSP-->>SS: Valid / Invalid
```

---

## Compartilhamento de validade durante troca de mensagens

```mermaid
sequenceDiagram
    participant SSA as Security Server A
    participant OCSP as OCSP Service
    participant SSB as Security Server B

    SSA->>OCSP: Check own certificate validity
    OCSP-->>SSA: Validity information

    SSA->>SSB: X-Road message + certificate validity info
    SSB->>SSB: Validate received information

    SSB-->>SSA: Response if certificates are valid
```

---

## Apenas servidores com certificados válidos trocam mensagens

```mermaid
flowchart TD
    SSA[Security Server A]
    SSB[Security Server B]

    SSA --> AC1[Valid Authentication Certificate]
    SSA --> SC1[Valid Signing Certificate]

    SSB --> AC2[Valid Authentication Certificate]
    SSB --> SC2[Valid Signing Certificate]

    AC1 --> EXCHANGE[Message Exchange Allowed]
    SC1 --> EXCHANGE
    AC2 --> EXCHANGE
    SC2 --> EXCHANGE
```

---

## Quando o certificado é inválido

```mermaid
flowchart TD
    SS[Security Server]

    SS --> CERT[Certificate]
    CERT --> CHECK[OCSP Validity Check]

    CHECK -->|Valid| OK[Continue X-Road Message Exchange]
    CHECK -->|Invalid| FAIL[Message Exchange Denied]
```

---

## Fluxo completo

```mermaid
flowchart TD
    CS[Central Server] --> TRUSTED[Trusted CAs List]

    TRUSTED --> CA[Certification Authority]

    CA --> AUTH[Authentication Certificates]
    CA --> SIGN[Signing Certificates]

    AUTH --> SSA[Security Server A]
    AUTH --> SSB[Security Server B]

    SIGN --> MEMBERA[X-Road Member A]
    SIGN --> MEMBERB[X-Road Member B]

    SSA --> OCSP[OCSP Validity Check]
    SSB --> OCSP

    OCSP --> VALID[Certificates Valid]

    VALID --> EXCHANGE[X-Road Message Exchange]
```

---

## Resumo visual

```mermaid
flowchart LR
    CA[Trusted CA] --> CERT[Certificates]
    CERT --> SS[Security Server]
    SS --> OCSP[OCSP Check]
    OCSP --> VALID[Valid Certificate]
    VALID --> XR[X-Road Message Exchange]
```
- - -
# Referências
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23560180-architecture