# Roadmap de Estudo — X-Road (Engenheiro de Software)

> Este roadmap está filtrado para o que importa no dia a dia de um **dev que vai integrar sistemas com X-Road**. Itens de administração de infra foram marcados como baixa prioridade.

---

## Fase 1 — Entender o terreno (Fundamentals)
> Objetivo: saber o que é X-Road, como funciona o fluxo de dados e onde você entra nessa história.

- [ ] **O que é X-Road** — conceito, propósito, por que existe
- [ ] **Arquitetura geral** — Security Server, Central Server, CA, TSA
- [ ] **Modelo organizacional** — quem é member, subsystem, operator
- [ ] **Fluxo de uma requisição** — passo a passo de consumer → security server → provider
- [ ] **Identificadores** — como funciona `INSTANCE/CLASS/MEMBER/SUBSYSTEM/SERVICE`
- [ ] **Protocolos suportados** — REST e SOAP, quando usar cada um
- [ ] **Segurança básica** — assinatura digital, mTLS, time-stamping, logging

> Curso: [X-Road Fundamentals](https://x-road.thinkific.com/courses/x-road-fundamentals)  
> ⏱ Estimativa: 3–5 horas

---

## Fase 2 — Desenvolver integrações (Service Developer)
> Objetivo: saber escrever código que consome e publica serviços via X-Road.

- [ ] **Networking do Security Server** — portas, endpoints, como o dev se conecta
- [ ] **Chaves e certificados** — o que o dev precisa saber (sem precisar configurar)
- [ ] **Comunicação com o sistema de informação** — como seu backend fala com o Security Server
- [ ] **Protocolo REST no X-Road** — headers obrigatórios, estrutura da URL, exemplos
- [ ] **Protocolo SOAP no X-Road** — estrutura do envelope, headers especiais
- [ ] **Metadata services** — como listar serviços disponíveis no X-Road
- [ ] **Erros** — como interpretar e tratar erros de retorno do X-Road
- [ ] **Interfaces de gestão** — Management REST API do Security Server (porta 4000)

> Curso: [X-Road Service Developer](https://x-road.thinkific.com/courses/x-road-service-developer)  
> ⏱ Estimativa: 2–4 horas

---

## Fase 3 — Contribuir com a plataforma (opcional — Core Developer)
> Objetivo: entender o código-fonte e contribuir para o X-Road open source.

- [ ] **Modelo de desenvolvimento** — papéis, change management, contribuições
- [ ] **Ambiente local** — LXD containers, Gradle build, configuração de instância
- [ ] **Controle de versão** — práticas de branching do projeto
- [ ] **Scripts Ansible** — como o deploy é feito
- [ ] **Trust services técnicos** — CA, OCSP, TSA com código

> Curso: [X-Road Core Developer](https://x-road.thinkific.com/courses/x-road-core-developer)  
> ⏱ Estimativa: 4–6 horas

---

## O que IGNORAR (por ora) como dev

| Tema | Por quê ignorar |
|---|---|
| Security Server Administration | Papel de DevOps/SRE, não dev |
| Central Server Administration | Papel do operador da rede |
| Configuração de infra/rede | Responsabilidade de infra |
| Ansible detalhado | Não é código de aplicação |

---

## Checklist de Conclusão

- [ ] Consigo explicar o que é X-Road em 2 minutos
- [ ] Sei montar a URL de uma chamada REST via X-Road
- [ ] Entendo o que é um `subsystem` e um `service code`
- [ ] Sei quais headers são obrigatórios numa requisição X-Road REST
- [ ] Sei ler e tratar um erro retornado pelo X-Road
- [ ] Consegui fazer uma chamada de teste no ambiente de desenvolvimento
