
A interface de **loopback** é uma interface virtual usada para comunicação interna dentro da própria máquina. Essas portas devem estar acessíveis localmente, mas não precisam ser expostas externamente.

|   |   |   |   |
|---|---|---|---|
|Componente|Portas|Protocolo|Observação|
|PostgreSQL database|5432|TCP|Porta padrão do PostgreSQL|
|Operational Monitoring daemon|2080|TCP|Monitoramento operacional|
|Environmental Monitoring daemon|2552|TCP|Monitoramento do ambiente|
|Signer|5559|TCP|Porta administrativa do Signer|
|Signer|5560|TCP|Porta gRPC do Signer|
|Proxy|5566|TCP|Porta administrativa do Proxy|
|Proxy|5567|TCP|Porta gRPC do Proxy|
|Configuration Client|5675|TCP|Porta administrativa do cliente de configuração|
|Audit Log|514|UDP|Logs de auditoria|
