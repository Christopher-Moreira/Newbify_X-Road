# 03 — Protocolo SOAP no X-Road

**Tags:** [[x-road]] [[API SOAP]]   
**Curso:** X-Road Service Developer — Módulo 4  
**Data:** 2026-06-24

---

## Quando você vai usar SOAP

- Integrações com sistemas **legados** que não suportam REST
- Quando o provider só oferece serviço SOAP
- Projetos governamentais mais antigos

> Para projetos novos, prefira REST. SOAP no X-Road é mais verboso.

---

## Diferença do SOAP padrão

No X-Road com SOAP, você:
1. Sempre envia para o **Security Server local** (não direto ao provider)
2. Adiciona um **header especial X-Road** no envelope SOAP
3. O endpoint do provider é definido **dentro do header**, não na URL

---

## Estrutura do envelope SOAP com X-Road

```xml
<soapenv:Envelope
  xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
  xmlns:xroad="http://x-road.eu/xsd/xroad.xsd"
  xmlns:id="http://x-road.eu/xsd/identifiers"
  xmlns:svc="http://seu-namespace-do-servico">

  <soapenv:Header>
    <!-- Identifica quem está chamando (consumer) -->
    <xroad:client id:objectType="SUBSYSTEM">
      <id:xRoadInstance>EE</id:xRoadInstance>
      <id:memberClass>COM</id:memberClass>
      <id:memberCode>11111111</id:memberCode>
      <id:subsystemCode>my-application</id:subsystemCode>
    </xroad:client>

    <!-- Identifica o serviço que está sendo chamado (provider) -->
    <xroad:service id:objectType="SERVICE">
      <id:xRoadInstance>EE</id:xRoadInstance>
      <id:memberClass>GOV</id:memberClass>
      <id:memberCode>99999999</id:memberCode>
      <id:subsystemCode>citizen-data</id:subsystemCode>
      <id:serviceCode>getPerson</id:serviceCode>
      <id:serviceVersion>v1</id:serviceVersion>
    </xroad:service>

    <!-- ID único da requisição -->
    <xroad:id>550e8400-e29b-41d4-a716-446655440000</xroad:id>

    <!-- Opcional: usuário final -->
    <xroad:userId>user@empresa.com</xroad:userId>
  </soapenv:Header>

  <soapenv:Body>
    <!-- Seu payload normal SOAP aqui -->
    <svc:getPerson>
      <svc:document>12345678901</svc:document>
    </svc:getPerson>
  </soapenv:Body>
</soapenv:Envelope>
```

### Enviando a requisição

```http
POST http://sec-server:8080/
Content-Type: text/xml; charset=UTF-8
SOAPAction: ""

<envelope acima>
```

---

## Perguntas para revisão

- [ ] Qual a principal diferença entre SOAP normal e SOAP no X-Road?
- [ ] O que vai na tag `<xroad:client>`?
- [ ] O que vai na tag `<xroad:service>`?
- [ ] Quando você prefere SOAP vs REST no X-Road?

---

## Links

- [[02 - Protocolo REST no X-Road]]
- [[04 - Erros e Troubleshooting]]
