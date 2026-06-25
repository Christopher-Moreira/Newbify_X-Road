# 03 — Identificadores e Códigos

**Tags:** [[x-road]] [[fundamentos]] [[identificadores]]  
**Curso:** X-Road Fundamentals — Módulo 2  
**Data:** 2026-06-24

---

## Por que isso importa para o dev?

Todo serviço X-Road é endereçado por um **identificador estruturado**. Você vai usar isso na URL de toda requisição. Entender essa estrutura é essencial.

---

## Estrutura do Identificador

```
INSTANCE / MEMBER_CLASS / MEMBER_CODE / SUBSYSTEM_CODE / SERVICE_CODE / VERSION
```

| Parte | O que é | Exemplo |
|---|---|---|
| `INSTANCE` | Identificador do ecossistema X-Road | `EE`, `FI`, `BR-GOV` |
| `MEMBER_CLASS` | Classe do membro (ex: GOV, COM, NGO) | `GOV`, `COM` |
| `MEMBER_CODE` | Código único da organização | `12345678` (CNPJ, por ex) |
| `SUBSYSTEM_CODE` | Sistema específico dentro da org | `population-registry` |
| `SERVICE_CODE` | Nome do serviço/endpoint | `getPersonData` |
| `VERSION` | Versão do serviço (opcional) | `v1` |

---

## Exemplo real — URL de chamada REST

```
http://SECURITY_SERVER:8080/r1/EE/GOV/12345678/population-registry/getPersonData/v1/pessoa/123
```

Quebrando em partes:
```
http://SECURITY_SERVER:8080   → seu Security Server local
/r1                           → prefixo do protocolo REST v1
/EE                           → instance
/GOV                          → member class
/12345678                     → member code (código da organização provider)
/population-registry          → subsystem code
/getPersonData                → service code
/v1                           → versão
/pessoa/123                   → path do endpoint real do serviço
```

---

## Níveis de identidade

```
X-Road Member (organização)
    └── Subsystem (sistema específico da org)
            └── Service (endpoint/operação)
                    └── Version (versão do serviço)
```

- **Membro** → quem você é no ecossistema
- **Subsystem** → qual dos seus sistemas está agindo (consumer ou provider)
- **Service** → o que você quer fazer

---

## Exemplo — headers HTTP obrigatórios (REST)

Além da URL, você precisa passar headers identificando quem está chamando:

```http
X-Road-Client: EE/GOV/12345678/my-subsystem
X-Road-Id: RANDOM-UUID-REQUEST-ID
```

- `X-Road-Client` → identifica seu subsystem (o consumer)
- `X-Road-Id` → ID único da requisição (para rastreamento nos logs)

---

## Perguntas para revisão

- [ ] Quais são as 6 partes de um identificador X-Road?
- [ ] Qual a diferença entre `MEMBER_CODE` e `SUBSYSTEM_CODE`?
- [ ] Por que o `X-Road-Client` header é obrigatório?
- [ ] Como seria o identificador do seu sistema se você fosse consumer?

---

## Links

- [[02 - Arquitetura e Componentes]]
- [[04 - Protocolos e Interfaces]]
- [[../Service Developer/02 - Protocolo REST no X-Road]]
