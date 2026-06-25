# 05 — Segurança e Confiança

**Tags:** [[x-road]] [[fundamentos]] [[segurança]] [[certificados]] [[mtls]]  
**Curso:** X-Road Fundamentals — Módulo 5  
**Data:** 2026-06-24

---

## O que o dev precisa saber (sem ser admin)

Você não vai configurar a segurança, mas precisa entender o que acontece por baixo para debugar problemas e entender os logs.

---

## Pilares de segurança do X-Road

### 1. Autenticação mútua (mTLS)
- A comunicação entre Security Servers usa **TLS mútuo**
- Ambos os lados apresentam certificado — ninguém "se faz passar" por outro
- Relevante para você: se a comunicação falha, pode ser um problema de cert

### 2. Assinatura digital
- Toda mensagem é **assinada digitalmente** pelo Security Server que a envia
- Garante integridade — qualquer alteração no conteúdo é detectada
- Você não precisa implementar isso — o Security Server faz automaticamente

### 3. Time-stamping
- Cada mensagem recebe um **timestamp confiável** da TSA
- Garante **não-repúdio** — ninguém pode negar que enviou ou recebeu algo
- Tem valor legal — pode ser usado como evidência em processos judiciais

### 4. Logging
- Todos os eventos são logados pelo Security Server
- Logs são protegidos — não podem ser alterados retroativamente
- Como dev: os logs são sua primeira fonte de debug

### 5. Controle de acesso
- O provider **controla quem pode chamar seus serviços**
- Acesso é concedido por subsystem — sua aplicação precisa estar registrada
- Se você recebe `403 Forbidden`, provavelmente é falta de permissão de acesso

---

## Certificados — o que o dev precisa saber

| Tipo | Para quê | Quem gerencia |
|---|---|---|
| Authentication cert | Autentica o Security Server na rede | Admin do Security Server |
| Signing cert | Assina as mensagens | Admin do Security Server |
| TLS cert | Comunica com seu sistema local | Pode ser você (dev) |

> **Como dev:** você precisa saber que esses certs existem. Se a integração falha com erro de TLS, chame quem administra o Security Server.

---

## Erros comuns de segurança que o dev encontra

| Erro | Provável causa |
|---|---|
| `503 - Server failed to produce a valid response` | Security Server do provider está offline |
| `403 - Access Denied` | Seu subsystem não tem permissão para acessar o serviço |
| `500 - Unknown member` | O identificador que você usou não existe no ecossistema |
| Erro de certificado TLS | Cert expirado ou configurado errado no Security Server |

---

## Perguntas para revisão

- [ ] O que é mTLS e por que o X-Road usa?
- [ ] Qual a diferença entre autenticação e assinatura no X-Road?
- [ ] Por que o time-stamping tem valor legal?
- [ ] Se você recebe `403`, qual é a causa mais provável?

---

## Links

- [[04 - Protocolos e Interfaces]]
- [[../Service Developer/04 - Erros e Troubleshooting]]
- [Arquitetura de Segurança Oficial](https://docs.x-road.global/Architecture/arc-sec_x_road_security_architecture.html)
