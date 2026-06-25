# 04 — Protocolos e Interfaces

**Tags:**  [[x-road]] [[fundamentos]] [[protocolo]] [[API REST]] [[API SOAP]]  
**Curso:** X-Road Fundamentals — Módulos 2 e 4  
**Data:** 2026-06-24

---

## Protocolos disponíveis

X-Road suporta dois protocolos de mensagens para comunicação entre sistemas:

| Protocolo | Quando usar |
|---|---|
| **REST** | Sistemas modernos, APIs JSON/HTTP — recomendado para novos projetos |
| **SOAP** | Sistemas legados, integrações com serviços antigos que usam WSDL |

---

## REST no X-Road

### Estrutura da URL
```
http://<security-server>:8080/r1/<INSTANCE>/<CLASS>/<MEMBER>/<SUBSYSTEM>/<SERVICE>[/<VERSION>][/<path>]
```

### Headers obrigatórios
```http
X-Road-Client: INSTANCE/CLASS/MEMBER_CODE/SUBSYSTEM
X-Road-Id: <uuid-único-por-requisição>
```

### Headers opcionais úteis
```http
X-Road-UserId: id-do-usuário-final      (para auditoria)
X-Road-Issue: código-do-chamado         (para rastreamento)
```

### Exemplo completo — GET
```http
GET http://sec-server:8080/r1/EE/GOV/99999999/hr-system/getEmployee/v1/employees/42
X-Road-Client: EE/COM/11111111/my-app
X-Road-Id: a3f2c1d4-9b8e-4a12-bc34-0123456789ab
```

---

## SOAP no X-Road

Menos comum em projetos novos, mas você pode encontrar em integrações legadas.

### Diferenças do SOAP padrão
- O X-Road adiciona um **header específico** no envelope SOAP
- O endpoint sempre aponta para o Security Server local, não o provider

```xml
<soapenv:Envelope>
  <soapenv:Header>
    <xroad:client>
      <xroad:xRoadInstance>EE</xroad:xRoadInstance>
      <xroad:memberClass>GOV</xroad:memberClass>
      <xroad:memberCode>99999999</xroad:memberCode>
      <xroad:subsystemCode>my-subsystem</xroad:subsystemCode>
    </xroad:client>
    <xroad:service>
      <!-- identificação do serviço provider -->
    </xroad:service>
    <xroad:id>REQUEST-UUID</xroad:id>
  </soapenv:Header>
  <soapenv:Body>
    <!-- payload normal do seu serviço -->
  </soapenv:Body>
</soapenv:Envelope>
```

---

## Management API (porta 4000)

O Security Server expõe uma **REST API administrativa** que permite:
- Listar membros e subsystems registrados
- Consultar serviços disponíveis
- Gerenciar certificados

```http
GET https://sec-server:4000/api/v1/clients
Authorization: Bearer <token>
```

> Como dev, você usará isso principalmente para **descobrir serviços disponíveis** e fazer debug.

---

## Perguntas para revisão

- [ ] Qual protocolo é recomendado para projetos novos?
- [ ] Quais headers são obrigatórios numa requisição REST X-Road?
- [ ] Para que serve o header `X-Road-Id`?
- [ ] Para que serve a Management API na porta 4000?

---

## Links

- [[03 - Identificadores e Códigos]]
- [[05 - Segurança e Confiança]]
- [[../Service Developer/02 - Protocolo REST no X-Road]]
- [[../Service Developer/03 - Protocolo SOAP no X-Road]]
