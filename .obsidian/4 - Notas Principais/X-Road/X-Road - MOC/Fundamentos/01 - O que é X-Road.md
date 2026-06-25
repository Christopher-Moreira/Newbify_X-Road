# 01 — O que é X-Road?

**Tags:** [[x-road]] [[fundamentos]]  
**Curso:** X-Road Fundamentals — Módulo 1  
**Data:** 2026-06-24

---

## Definição

X-Road é uma **camada de troca de dados distribuída e open source** que permite que organizações troquem informações de forma segura pela internet.

> Em vez de dois sistemas se conectarem diretamente, ambos se conectam através de um **Security Server** padronizado — e é esse servidor que garante segurança, rastreabilidade e integridade.

---

## Por que existe?

Governos e empresas precisam trocar dados entre sistemas diferentes (ex: receita federal ↔ banco ↔ prefeitura). Fazer isso de forma segura, padronizada e rastreável é difícil. X-Road resolve isso com uma infra comum.

> Nasceu na Estônia (governo digital), hoje é usado em vários países e empresas.

---

## Analogia

Pense no X-Road como os **Correios com AR (aviso de recebimento) e cofre criptografado**:
- Você (sistema) manda uma carta (requisição)
- Ela passa pelo seu posto (Security Server consumer)
- Viaja criptografada pela internet
- Chega no posto do destinatário (Security Server provider)
- É entregue com assinatura e registro de horário
- Ninguém no meio consegue ler ou alterar o conteúdo

---

## O que X-Road **não é**

| ❌ Não é | ✅ É |
|---|---|
| Um message broker centralizado (tipo Kafka) | Uma camada distribuída — dados vão direto de A para B |
| Uma API Gateway | Um middleware de segurança e identidade |
| Um banco de dados | Apenas transporte seguro |
| Algo que guarda seus dados | Os dados ficam nos sistemas de cada organização |

---

## Casos de uso típicos

- Governo consulta dados de cidadão em outro órgão
- Empresa acessa serviço de outra empresa de forma auditável
- Integração B2B com rastreabilidade legal (não-repúdio)
- Plataformas nacionais de interoperabilidade

---

## Perguntas para revisão

- [ ] O que é X-Road em uma frase?
- [ ] Por que usar X-Road em vez de uma API direta?
- [ ] X-Road é centralizado ou distribuído?
- [ ] Quem tem acesso aos dados trafegados pelo X-Road?

---

## Links

- [[02 - Arquitetura e Componentes]]
- [X-Road Technology Overview](https://x-road.global/x-road-technology-overview)
