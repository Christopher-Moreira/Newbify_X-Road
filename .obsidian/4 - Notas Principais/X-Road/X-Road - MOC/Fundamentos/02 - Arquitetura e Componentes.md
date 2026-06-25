# 02 — Arquitetura e Componentes

**Tags:** [[x-road]] [[fundamentos]] [[arquitetura]]  
**Curso:** X-Road Fundamentals — Módulo 4  
**Data:** 2026-06-24

---

## Componentes do ecossistema X-Road

```
┌──────────────────────────────────────────────────────────┐
│                    X-Road Ecosystem                       │
│                                                          │
│  [Central Server]  ←── gerencia membros e política       │
│         │                                                │
│    [CA / TSA]  ←── emite certificados e timestamps       │
│                                                          │
│  Organização A                    Organização B          │
│  ┌─────────────────┐              ┌─────────────────┐    │
│  │  Seu Sistema    │              │  Sistema Deles  │    │
│  │  (Consumer)     │              │  (Provider)     │    │
│  └────────┬────────┘              └────────┬────────┘    │
│           │                               │              │
│  ┌────────▼────────┐    mTLS     ┌────────▼────────┐    │
│  │ Security Server │ ──────────► │ Security Server │    │
│  │   (Consumer)    │             │   (Provider)    │    │
│  └─────────────────┘             └─────────────────┘    │
└──────────────────────────────────────────────────────────┘
```

---

## Componentes — o que cada um faz

### Security Server
> **O mais importante para o dev entender**

- Porta de entrada obrigatória para todo membro X-Road
- Faz: assinatura digital, criptografia, logging, time-stamping
- Expõe para o seu sistema uma interface REST ou SOAP simples
- Seu código fala **com o Security Server local**, não diretamente com o outro sistema
- Porta padrão para consumidores: `8080` (HTTP) ou `8443` (HTTPS)
- Porta da Management API: `4000`

### Central Server
- Mantém o registro de todos os membros e Security Servers
- Distribui a **configuração global** (lista de CAs confiáveis, TSAs, políticas)
- Não participa das trocas de dados em tempo real — é consultado em background

### CA (Certification Authority)
- Emite os certificados que provam a identidade de cada organização
- Necessário para entrar no ecossistema X-Road
- Como dev: você precisa saber que o certificado existe, não precisa gerenciá-lo

### TSA (Time-Stamping Authority)
- Carimba cada mensagem com um timestamp confiável
- Garante **não-repúdio**: ninguém pode negar que enviou/recebeu algo
- Relevante para auditoria e disputas legais

---

## Fluxo de uma requisição (passo a passo)

```
1. Seu sistema → envia request ao Security Server LOCAL (porta 8080)
2. Security Server consumer → assina + loga a requisição
3. Security Server consumer → envia via mTLS para o Security Server do provider
4. Security Server provider → verifica + loga + encaminha para o sistema do provider
5. Sistema provider → processa e retorna a resposta
6. Security Server provider → assina + loga a resposta
7. Security Server consumer → verifica + loga + retorna para o seu sistema
8. Seu sistema → recebe a resposta
```

> **O que isso significa para o dev:** você só precisa se preocupar com os passos 1 e 8. O resto é transparente.

---

## Perguntas para revisão

- [ ] Quais são os 4 componentes principais do ecossistema X-Road?
- [ ] Qual componente seu código precisa conhecer diretamente?
- [ ] Por que o Central Server não precisa estar online para a troca de dados?
- [ ] O que o TSA garante?

---

## Links

- [[01 - O que é X-Road]]
- [[03 - Identificadores e Códigos]]
- [Arquitetura Técnica Oficial](https://docs.x-road.global/Architecture/arc-g_x-road_arhitecture.html)
