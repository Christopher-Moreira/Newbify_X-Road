# 02 — Protocolo REST no X-Road

**Tags:** [[x-road]] [[API REST]] [[protocolo]]  
**Curso:** X-Road Service Developer — Módulo 4  
**Data:** 2026-06-24

---

## Estrutura completa de uma requisição REST

```
http://<security-server>:<porta>/r1/<INSTANCE>/<CLASS>/<MEMBER>/<SUBSYSTEM>/<SERVICE>[/<VERSION>][/<path>]
```

### Decodificando cada parte

| Parte | Descrição |
|---|---|
| `/r1` | Prefixo obrigatório — indica protocolo REST v1 |
| `INSTANCE` | Ecossistema X-Road (ex: `EE`, `BR`) |
| `CLASS` | Classe do membro provider (ex: `GOV`, `COM`) |
| `MEMBER` | Código do membro provider (ex: CNPJ) |
| `SUBSYSTEM` | Subsystem do provider |
| `SERVICE` | Service code registrado no X-Road |
| `VERSION` | Versão (opcional, ex: `v1`) |
| `path` | O path real do endpoint no sistema provider |

---

## Headers HTTP obrigatórios

```http
X-Road-Client: INSTANCE/CLASS/MEMBER_CODE/SUBSYSTEM_CODE
X-Road-Id: <uuid-único>
```

## Headers opcionais (boas práticas)

```http
X-Road-UserId: <id-do-usuário>        # auditoria de quem acionou
X-Road-Issue: <referência>            # rastreabilidade de negócio
```

---

## Exemplos práticos

### GET — buscar recurso

```http
GET http://sec-server:8080/r1/EE/GOV/99999999/citizen-data/getPerson/v1/persons/12345678901
X-Road-Client: EE/COM/11111111/my-application
X-Road-Id: 550e8400-e29b-41d4-a716-446655440000
X-Road-UserId: user@empresa.com
```

### POST — criar recurso

```http
POST http://sec-server:8080/r1/EE/GOV/99999999/citizen-data/createRecord/v1/records
X-Road-Client: EE/COM/11111111/my-application
X-Road-Id: 550e8400-e29b-41d4-a716-446655440001
Content-Type: application/json

{
  "name": "João Silva",
  "document": "12345678901"
}
```

---

## Metadata Services — descobrir o que está disponível

O X-Road oferece endpoints especiais para listar serviços:

```http
# Listar todos os métodos de um subsystem
GET http://sec-server:8080/r1/EE/GOV/99999999/citizen-data/listMethods

# Obter o OpenAPI spec de um serviço
GET http://sec-server:8080/r1/EE/GOV/99999999/citizen-data/getOpenAPI?serviceCode=getPerson
```

---

## Resposta do X-Road

A resposta que você recebe é a resposta original do sistema provider, transparente. O X-Road não altera o body. Headers adicionados pelo X-Road na resposta:

```http
X-Road-Id: <mesmo id da requisição>
X-Road-Request-Id: <id interno do X-Road>
```

---

## Perguntas para revisão

- [ ] O que significa o prefixo `/r1` na URL?
- [ ] Quais headers são obrigatórios numa requisição REST X-Road?
- [ ] Como você descobre quais serviços um subsystem oferece?
- [ ] O X-Road modifica o body da resposta?

---

## Links

- [[01 - Security Server para Desenvolvedores]]
- [[03 - Protocolo SOAP no X-Road]]
- [[04 - Erros e Troubleshooting]]
- [[../Fundamentos/03 - Identificadores e Códigos]]
