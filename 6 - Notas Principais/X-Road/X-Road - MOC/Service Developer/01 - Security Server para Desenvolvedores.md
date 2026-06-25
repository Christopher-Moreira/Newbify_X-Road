# 01 — Security Server para Desenvolvedores

**Tags:** [[x-road]] [[#Como Consumer (consumindo um serviço)]] [[X Road - Security Server]]  
**Curso:** X-Road Service Developer — Módulo 2  
**Data:** 2026-06-24

---

## Visão do dev — o Security Server como "proxy"

Para o seu código, o Security Server é essencialmente um **proxy reverso com superpoderes de segurança**.

```
Seu código → Security Server (local) → [magia X-Road] → Security Server (remoto) → Sistema provider
```

Você manda uma requisição HTTP normal. O Security Server cuida de tudo mais.

---

## Portas e endpoints

| Porta | Protocolo | Para quê |
|---|---|---|
| `8080` | HTTP | Consumer envia requisições REST (desenvolvimento) |
| `8443` | HTTPS | Consumer envia requisições REST (produção) |
| `4000` | HTTPS | Management API (admin, listagem de serviços) |
| `5500` | HTTPS | Comunicação entre Security Servers (não é você) |
| `5577` | HTTPS | Comunicação entre Security Servers (OCSP) |

> **Na prática:** você vai usar `8080` ou `8443`. O resto é infra.

---

## Como seu sistema se conecta

### Como Consumer (consumindo um serviço)

```
Seu Sistema → POST/GET http://sec-server:8080/r1/{identificador-do-serviço}
                         com headers X-Road-Client e X-Road-Id
```

### Como Provider (publicando um serviço)

```
Security Server → chama seu sistema em http://seu-sistema/seu-endpoint
                  com um header X-Road-Client identificando quem chamou
```

> Quando você é **provider**, seu sistema recebe a chamada do Security Server local. Você não precisa fazer nada diferente de uma API REST normal — mas pode ler o header `X-Road-Client` para saber quem está chamando.

---

## Interfaces do Security Server que o dev usa

### 1. Proxy Interface (porta 8080/8443)
- Recebe suas requisições de serviço
- Você aponta sua chamada aqui

### 2. Management REST API (porta 4000)
- Listar clientes e subsystems registrados
- Listar serviços disponíveis
- Monitorar status

```http
# Listar todos os clientes registrados
GET https://sec-server:4000/api/v1/clients
Authorization: Bearer <api-key>

# Listar serviços de um cliente
GET https://sec-server:4000/api/v1/clients/{id}/service-descriptions
```

---

## Monitoramento (relevante para debug)

O Security Server expõe métricas operacionais via:
- **Environmental monitoring** — estado do sistema
- **Operational monitoring** — estatísticas de requisições (contagem, tempos de resposta)

Útil para entender se o problema está no seu código ou na infra do X-Road.

---

## Perguntas para revisão

- [ ] Qual porta você usa para enviar requisições como consumer?
- [ ] Quando você é **provider**, como seu sistema recebe a chamada?
- [ ] Para que serve a Management API na porta 4000?
- [ ] Como você sabe, como provider, quem está chamando seu serviço?

---

## Links

- [[../Fundamentos/04 - Protocolos e Interfaces]]
- [[02 - Protocolo REST no X-Road]]
