# 04 — Erros e Troubleshooting

**Tags:** [[x-road]]  [[debug]]  
**Curso:** X-Road Service Developer — Módulo 4  
**Data:** 2026-06-24

---

## Tipos de erro no X-Road

Os erros podem vir de dois lugares:
1. **X-Road Infrastructure** — o próprio X-Road retorna um erro (antes de chegar ao provider)
2. **Sistema Provider** — o sistema do outro lado retornou um erro HTTP normal

---

## Erros REST — formato de resposta do X-Road

Quando o X-Road em si falha (não o provider), você recebe:

```json
{
  "type": "https://x-road.eu/fault/server",
  "title": "Server.ServerProxy.UnknownService",
  "detail": "Unknown service: EE/GOV/99999999/citizen-data/getPerson/v1",
  "instance": "https://sec-server:8443/r1/..."
}
```

---

## Erros SOAP — formato de resposta do X-Road

```xml
<soapenv:Fault>
  <faultcode>Server.ServerProxy.UnknownService</faultcode>
  <faultstring>Unknown service: ...</faultstring>
  <detail>
    <xroad:faultDetail>Detalhes do erro aqui</xroad:faultDetail>
  </detail>
</soapenv:Fault>
```

---

## Tabela de erros comuns

| Código / Mensagem | Causa | O que fazer |
|---|---|---|
| `Server.ServerProxy.UnknownService` | Serviço não existe ou identificador errado | Verificar o identificador (instance/class/member/subsystem/service) |
| `Server.ServerProxy.AccessDenied` | Seu subsystem não tem permissão | Pedir ao admin do provider para liberar acesso |
| `Server.ServerProxy.ServiceFailed` | O sistema provider retornou erro | Verificar com o time do provider |
| `Server.ClientProxy.IOError` | Falha de rede/timeout | Verificar conectividade com o Security Server |
| `Server.ServerProxy.UnknownMember` | Membro não registrado no ecossistema | Verificar se o MEMBER_CODE está correto |
| `503 Service Unavailable` | Security Server fora do ar | Verificar status do Security Server |

---

## Passo a passo de debug

```
1. Verifique se o identificador está 100% correto (typo é causa #1)
   → INSTANCE / CLASS / MEMBER / SUBSYSTEM / SERVICE / VERSION

2. Verifique se o header X-Road-Client está correto
   → Deve corresponder ao seu subsystem cadastrado

3. Verifique se o Security Server local está respondendo
   → curl http://sec-server:8080/ (deve retornar algo)

4. Verifique permissões de acesso
   → Fale com o admin do provider se receber AccessDenied

5. Consulte os logs do Security Server
   → /var/log/xroad/ (se você tiver acesso ao servidor)
   → Ou peça ao admin os logs do período

6. Verifique a Management API
   → GET https://sec-server:4000/api/v1/clients (confirma o que está registrado)
```

---

## Dica: testar com curl

```bash
curl -v \
  -H "X-Road-Client: EE/COM/11111111/my-application" \
  -H "X-Road-Id: test-$(date +%s)" \
  http://sec-server:8080/r1/EE/GOV/99999999/citizen-data/listMethods
```

Usar `listMethods` primeiro para confirmar que o serviço existe antes de chamar o endpoint real.

---

## Perguntas para revisão

- [ ] Como diferenciar um erro do X-Road de um erro do provider?
- [ ] Qual o erro mais comum quando o identificador está errado?
- [ ] O que fazer quando receber `AccessDenied`?
- [ ] Como testar se um serviço existe antes de chamar?

---

## Links

- [[02 - Protocolo REST no X-Road]]
- [[../Fundamentos/05 - Segurança e Confiança]]
