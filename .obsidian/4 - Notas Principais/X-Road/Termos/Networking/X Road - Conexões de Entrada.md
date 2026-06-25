
|   |   |   |   |   |
|---|---|---|---|---|
|Origem|Destino|Portas|Protocolo|Observação|
|Monitoring Security Server|Security Server|5500, 5577|TCP|Necessário para monitoramento|
|Data Exchange Partner Security Server|Security Server|5500, 5577|TCP|Entrada de chamadas de parceiros|
|ACME Server|Security Server|80|TCP|Utilizado pelo ACME|
|Consumer Information System|Security Server|8080, 8443|TCP|Origem na rede interna|
|Admin|Security Server|4000|TCP|Acesso administrativo pela rede interna|
