2026-06-27
Status: #adult

Tags: [[x-road]] [[programação]] [[relatórios]] [[estudo]]

- - -
# Introduction

## Welcome

### Ideias iniciais e entendimento:

Essa anotação continua o estudo sobre [[x-road]], mas agora com foco mais direto no [[X Road - Security Server]].
A ideia principal desse módulo é entender como o Security Server funciona por dentro, como ele é instalado, configurado, monitorado e operado no dia a dia.
No estudo anterior, o X-Road foi entendido como uma camada de interoperabilidade segura entre organizações. Aqui o foco passa a ser o componente que torna essa troca possível na prática: o **Security Server**.

> [!note]- Resumo rápido
> O [[X Road - Security Server]] é o componente que intermedia as chamadas entre sistemas consumidores e provedores, aplicando segurança, assinatura, autenticação, logs, time-stamping e validação de certificados.

---
## Getting started
### Primeira visão do módulo

O Security Server é o principal ponto de contato entre os sistemas de informação e a infraestrutura X-Road.
Ele atua como uma espécie de camada de segurança e mediação entre:
- O sistema consumidor de serviço;

- O sistema provedor de serviço;

- Outros Security Servers;

- Componentes centrais como [[X Road - Central Services]], OCSP, TSA e configuração global.
Fluxo mental básico:
```text
Client Information System

        ↓

Client Security Server

        ↓

X-Road Transport Protocol

        ↓

Provider Security Server

        ↓

Provider Information System

```

Ou seja, o sistema cliente não chama diretamente o sistema provedor. Ele chama seu próprio Security Server, que resolve o destino e troca mensagens com o Security Server do provedor.

---
## Technology overview

### Ideias iniciais

Tecnicamente, o X-Road é uma infraestrutura distribuída de troca de dados.
A comunicação é feita diretamente entre Security Servers, sem que um serviço central intermedie todas as mensagens. Isso torna o ecossistema mais resiliente, porque a falha de um Security Server afeta apenas os serviços hospedados nele, não todo o ambiente.
O Security Server trabalha com:

- Protocolos de mensagem para SOAP e REST;

- Protocolo de transporte entre Security Servers;

- Assinaturas digitais;

- Certificados de autenticação e assinatura;

- OCSP para validade de certificados;

- TSA para carimbo de tempo;

- Logs técnicos, logs de negócio e logs de auditoria;

- API REST de gerenciamento;

- Interface administrativa web.

> [!attention]- [[Ponto de Atenção]]

> O X-Road não é apenas um API Gateway. Ele até pode parecer um gateway em uma primeira leitura, mas o ponto forte dele está em interoperabilidade segura, não repúdio, trilha de auditoria e confiança entre organizações.

---
## Organisational model
### Member
Um **Member** representa uma organização participante do X-Road.
Um membro pode estar associado a vários Security Servers. Ao mesmo tempo, um Security Server pode estar associado a no máximo dois membros simultaneamente.

Exemplo mental:
```text

MEMBER:EE/BUSINESS/123456789

```
Esse identificador representa uma organização da instância `EE`, da classe `BUSINESS`, com código `123456789`.

---
### Subsystem
Um **Subsystem** representa uma parte do sistema de informação de um membro.
Os subsistemas são importantes porque os direitos de acesso são independentes. Ou seja, liberar acesso para um subsistema não libera automaticamente acesso para os outros subsistemas do mesmo membro.

Exemplo:
```text

SUBSYSTEM:EE/BUSINESS/123456789/highsecurity

```
Nesse caso, `highsecurity` é o subsistema pertencente ao membro `EE/BUSINESS/123456789`.

---
### Service
Um **Service** é uma API ou serviço SOAP/REST fornecido por um membro ou subsistema.

Exemplo:
```text

SERVICE:EE/BUSINESS/123456789/highsecurity/getSecureData

```
Esse identificador representa o serviço `getSecureData`, fornecido pelo subsistema `highsecurity`.

---
### Direitos de acesso
Os direitos de acesso podem ser concedidos para:
- Um subsistema;

- Um grupo global de direitos de acesso;

- Um grupo local de direitos de acesso.

Para serviços SOAP, o controle costuma acontecer no nível do serviço.
Para serviços REST, o controle pode acontecer no nível da API ou no nível do endpoint.

> [!note]- [[Nota Mental]]
> Para REST, se o cliente tem acesso no nível da API, ele pode acessar todos os endpoints daquela API. O Security Server não trabalha com negação explícita, apenas com liberação de acesso.

---
## Architecture
### Visão geral
A arquitetura do X-Road é composta por componentes centrais e componentes instalados nos ambientes dos participantes.
Os principais componentes são:
- [[X Road - Central Services]];

- [[X Road - Security Server]];

- [[X Road - Information System]];

- [[X Road - Time-Stamping Authority (TSA)]];

- [[X Road - Certification Authority (CA)]];

- [[X Road - Configuration Proxy]];

- [[X Road - Operational Monitoring]];

- [[X Road - Environmental Monitoring]].

Tudo que não faz parte do X-Road Core normalmente aparece em cinza nos diagramas oficiais.

---
### Central Server
O **Central Server** gerencia a base de membros e Security Servers registrados em uma instância X-Road.
Ele também mantém a política de segurança da instância, incluindo:
- Autoridades certificadoras confiáveis;

- Autoridades de time-stamping confiáveis;

- Parâmetros de segurança;

- Tempo máximo de validade de respostas OCSP;

- Configuração global.

A configuração global é distribuída para os Security Servers via HTTP.

---
### Security Server
O **Security Server** media chamadas de serviço e respostas entre sistemas de informação.
Ele encapsula os aspectos de segurança do X-Road:
- Gerenciamento de chaves;

- Assinatura de mensagens;

- Autenticação entre Security Servers;

- Comunicação segura;

- Time-stamping;

- Logs;

- Validação de certificados;

- Suporte a SOAP e REST.

Um único Security Server pode hospedar várias organizações, funcionando em modelo **multi-tenant**.

---
### Information System
O **Information System** é o sistema real da organização.
Ele pode atuar como:
- Consumidor de serviços;

- Provedor de serviços;

- Ambos.

No lado consumidor, ele usa o Security Server como ponto de entrada para acessar serviços X-Road.
No lado provedor, ele implementa uma API SOAP ou REST e a disponibiliza por meio do Security Server.

---
## Community
### Comunidade e ecossistema
O X-Road é mantido e desenvolvido dentro de um ecossistema aberto, com documentação pública, repositórios e materiais de treinamento.
Alguns pontos importantes:
- O X-Road possui documentação oficial aberta;

- Existem guias de instalação, operação e desenvolvimento;

- O código e extensões aparecem em repositórios do Nordic Institute for Interoperability Solutions;

- Existem extensões como o `REST Adapter Service`;

- A comunidade contribui com melhorias, correções e discussões sobre interoperabilidade.

> [!note]- [[Nota Mental]]
> Vale separar o estudo entre “usar X-Road como dev de serviço” e “operar X-Road como administrador de Security Server”. Esse markdown está mais voltado para a segunda parte.

---
# Architecture

## Introduction

### Ideias iniciais e entendimento:
Essa parte entra na arquitetura interna do [[X Road - Security Server]].
Aqui o foco não é mais apenas entender o que é o X-Road, mas entender quais processos, serviços, portas, certificados, logs e modelos de implantação fazem o Security Server funcionar.
O Security Server é dividido em componentes internos, cada um com uma função específica.
Fluxo geral:
```text

Information System

        ↓

Proxy / Message Protocol

        ↓

Security Server internals

        ↓

Transport Protocol

        ↓

Another Security Server

```

---
## Component view
### Visão de componentes
O Security Server é composto por vários componentes internos.
Os principais são:
- Proxy;

- Message Log;

- Metadata Services;

- Signer;

- Database;

- User Interface Frontend;

- Management REST API;

- Configuration Client;

- Operational Monitoring Services;

- Opmonitor;

- Environmental Monitoring Service;

- Monitor;

- Password Store;

- SSCD.

---
### Proxy
O **Proxy** é responsável por mediar mensagens entre clientes e provedores de serviço.
Ele transmite mensagens pela rede e garante que a comunicação seja protegida por:
- Assinaturas digitais;

- Criptografia;

- Autenticação;

- Validação de mensagens.

É um dos componentes mais importantes do Security Server.

---
### Message Log
O **Message Log** registra as mensagens regulares que passam pelo Security Server.
Essas mensagens são armazenadas com suas assinaturas e depois recebem time-stamping.
O objetivo é permitir comprovar, para terceiros, que determinada requisição ou resposta foi recebida/processada.
Também realiza:
- Arquivamento periódico dos logs;

- Remoção de registros arquivados do banco;

- Armazenamento em documentos assinados;

- Criptografia opcional de registros e arquivos.

---
### Metadata Services
Os **Metadata Services** fornecem métodos para descobrir quais serviços estão disponíveis em um Security Server.
Eles são usados por participantes X-Road para descobrir:
- Clientes;

- Serviços;

- Métodos permitidos;

- Descrições WSDL;

- Descrições OpenAPI.

---
### Signer
O **Signer** gerencia chaves e certificados usados para assinatura de mensagens.
Ele é chamado pelo Proxy para:
- Assinar mensagens;

- Verificar assinaturas;

- Gerenciar chaves;

- Gerar CSRs;

- Trabalhar com tokens e HSMs.

---
### Database
A configuração do Security Server é armazenada em um banco **PostgreSQL**.
A configuração pode ser alterada por:
- Interface web;

- Management REST API.

O banco pode ser local ou remoto.

---
### User Interface Frontend
A interface web do Security Server permite gerenciar a configuração.
Ela funciona como uma aplicação web SPA que consome a **Management REST API**.

---
### Management REST API
A **Management REST API** expõe endpoints para ler e alterar configurações do Security Server.
Ela é usada pela interface web e também pode ser usada por clientes externos.
Também se comunica com:
- ACME server da CA;

- Central Server;

- Signer;

- Banco PostgreSQL;

- Configuration Client.

> [!attention]- [[Ponto de atenção]]
> Algumas operações da Management REST API geram requisições de gerenciamento para o Central Server e precisam ser aprovadas pelo administrador central antes de entrarem na configuração global.

---
### Configuration Client
O **Configuration Client** baixa a configuração global remota.
A origem da configuração vem do arquivo **anchor**, carregado na interface do Security Server.
Ele baixa a configuração do:
- Central Server;

- Configuration Proxy.

---
### Operational Monitoring Services
Fornece métodos para participantes X-Road consultarem dados de monitoramento operacional do Security Server.
Ele consulta o serviço local `opmonitor` e retorna os dados via SOAP XML.

---
### Opmonitor
O **Opmonitor** coleta dados operacionais, como:
- Quais serviços foram chamados;

- Quantas vezes foram chamados;

- Tamanho da resposta;

- Sucesso/falha;

- Tempo de processamento.

---
### Environmental Monitoring Service
Fornece métodos para consultar dados ambientais do Security Server.
Ele consulta o serviço local de monitoramento e traduz os dados para uma resposta SOAP XML.

---
### Monitor
O **Monitor** coleta dados ambientais, como:
- Processos em execução;

- Espaço em disco;

- Pacotes instalados;

- Uso de memória;

- Informações do ambiente.

---
### Password Store
O **Password Store** armazena senhas de tokens de segurança em memória compartilhada do sistema operacional.
Isso permite que o login em tokens persista até o Security Server ser reiniciado.

---
### SSCD
O **SSCD** é um componente opcional de hardware usado para criação segura de assinaturas criptográficas.
Ele precisa ser compatível com **PKCS #11**.
Pode ser usado quando o ambiente exige maior proteção das chaves privadas.

---
## Process view
### Visão de processos
Além dos componentes conceituais, o Security Server roda processos de sistema específicos.
Os principais processos são:
- `xroad-proxy-ui-api`;

- `xroad-signer`;

- `xroad-confclient`;

- `xroad-proxy`;

- `postgresql`;

- `xroad-monitor`;

- `xroad-opmonitor`;

- `xroad-addon-messagelog`.

---
### xroad-proxy-ui-api
O `xroad-proxy-ui-api` fornece:
- Interface administrativa web;

- Management REST API;

- Interface ACME para receber challenge requests.

Ele lê e grava dados como:
- Arquivo anchor;

- Backups de configuração;

- Certificado TLS interno;

- Configuração no banco `serverconf`;

- Configuração global no filesystem.

---
### xroad-signer
O `xroad-signer` gerencia:
- Tokens;

- Chaves;

- Certificados;

- Keystore;

- `keyconf.xml`.

Também busca informações no OCSP responder da CA.
Arquivo importante:
```text

/etc/xroad/signer/keyconf.xml

```

---
### xroad-confclient
O `xroad-confclient` é responsável por buscar a configuração global.
Ele baixa a configuração de uma fonte como:
- Central Server;

- Configuration Proxy.

Depois, armazena os arquivos localmente em disco para serem usados por outros processos.

---
### xroad-proxy
O `xroad-proxy` é o processo mais importante do Security Server.
Ele é responsável por:
- Transmitir mensagens;

- Receber mensagens da rede interna;

- Receber mensagens da rede externa;

- Fazer message logging;

- Fazer time-stamping;

- Expor interface de administração interna;

- Operar em modo de manutenção quando necessário.

> [!attention]- [[Ponto de atenção]]
> O arquivamento e limpeza dos logs não ficam no `xroad-proxy`, mas sim no `xroad-addon-messagelog`.

---
### postgresql
O PostgreSQL é o armazenamento persistente principal do Security Server.
Ele é usado por:
- `xroad-proxy-ui-api`;

- `xroad-proxy`;

- `xroad-opmonitor`.

---
### xroad-monitor
O `xroad-monitor` coleta monitoramento ambiental.
Exemplos:
- Processos;

- Memória;

- Status de certificados;

- Informações do sistema.

Os dados ficam em memória do processo.

---
### xroad-opmonitor
O `xroad-opmonitor` coleta monitoramento operacional.
Ele armazena dados em PostgreSQL e também trabalha com buffers no processo `xroad-proxy`.

---
### xroad-addon-messagelog

O `xroad-addon-messagelog` faz:
- Arquivamento do message log;

- Limpeza dos logs do banco;

- Persistência de arquivos em disco;

- Operação sobre o banco `messagelog`.

---
## Keys and certificates
### Ideias iniciais
A partir da versão **7.0.0**, o Security Server passou a trabalhar com múltiplas chaves de criptografia.
Essas chaves são divididas em duas categorias:
- Chaves para proteger dados em trânsito;

- Chaves para proteger dados em repouso.

---
### Dados em trânsito
Dados em trânsito são os dados trafegando entre Security Servers, sistemas de informação, interfaces administrativas ou APIs.
O Security Server possui cinco tipos principais de chaves/certificados para esse caso.

---
#### 1. Authentication key and certificate
A **authentication key** e o **authentication certificate** certificam a autenticidade de um Security Server.
São usados nas conexões entre Security Servers.
As chaves de autenticação são sempre armazenadas no **soft token**.

---
#### 2. Sign key and certificate
A **sign key** e o **sign certificate** certificam a autenticidade de um membro X-Road.

São usados para:
- Assinar mensagens;

- Verificar integridade;

- Criar prova digital das mensagens.

Podem ser armazenados em:
- Soft token;

- Security token;

- HSM.

---
#### 3. Internal TLS certificate
O **internal TLS certificate** é usado na comunicação entre o Security Server e um sistema de informação.
Pode atuar como certificado de cliente ou servidor dependendo do papel exercido na conexão.

---
#### 4. UI/API TLS certificate
O **UI/API TLS certificate** é usado para acesso à interface administrativa e à Management REST API.
Porta padrão:
```text

4000

```

A chave TLS e o certificado autoassinado são gerados automaticamente durante a instalação.

---
#### 5. API Keys
As **API Keys** autenticam chamadas feitas para a Management REST API.
Elas são associadas a roles que definem permissões.

---
### Dados em repouso
Dados em repouso são dados armazenados em disco, banco, backups e arquivos de log.
O Security Server possui quatro tipos principais de chaves para proteger esses dados.

---
#### 1. Internal GPG key
A **internal GPG key** é usada em backups e arquivos de log.
A chave privada é usada para:
- Assinar backups;

- Assinar arquivos de log arquivados;

- Descriptografar backups durante restore.

A chave pública é usada para:
- Criptografar backups;

- Criptografar arquivos de log;

- Verificar integridade de backups

---
#### 2. Backup/restore GPG keys
São chaves públicas adicionais usadas para criptografar backups quando a criptografia está habilitada.

---
#### 3. Database encryption key
É usada para criptografar o corpo das mensagens no banco de message log.

---
#### 4. Message log archive encryption keys
São chaves públicas adicionais por membro, usadas para criptografar arquivos de log arquivados.
Essas chaves são específicas por membro.

---
## Networking
### Ideias iniciais
É fortemente recomendado proteger o Security Server com firewall.
O firewall pode ser aplicado em conexões:

- De entrada;

- De saída;

- Internas;

- Externas.

A recomendação é permitir apenas portas necessárias e restringir origem por IP sempre que possível

---
### Portas principais
Para monitoramento e troca entre Security Servers, algumas portas aparecem com frequência:
```text

5500/tcp

5577/tcp

```
Essas portas são importantes para:
- Troca de mensagens entre Security Servers;

- Download de respostas OCSP;

- Monitoramento pelo operador X-Road.

---
### Conexões de saída
Exemplos de conexões de saída do Security Server:

| Origem          | Destino                     | Portas              | Uso                                 |
| --------------- | --------------------------- | ------------------- | ----------------------------------- |
| Security Server | Central Server              | `80`, `443`, `4001` | Configuração global e gerenciamento |
| Security Server | OCSP Service                | `80 / 443`          | Validade de certificados            |
| Security Server | TSA                         | `80 / 443`          | Time-stamping                       |
| Security Server | Partner Security Server     | `5500`, `5577`      | Troca de dados e OCSP               |
| Security Server | Producer Information System | `80`, `443`, outras | Chamada ao sistema provedor         |
| Security Server | ACME Server                 | `80 / 443`          | Certificados via ACME               |

---
### Conexões de entrada
Exemplos de conexões de entrada:

| Origem                      | Destino         | Portas         | Uso                   |
| --------------------------- | --------------- | -------------- | --------------------- |
| Partner Security Server     | Security Server | `5500`, `5577` | Troca de dados        |
| Monitoring Security Server  | Security Server | `5500`, `5577` | Monitoramento         |
| ACME Server                 | Security Server | `80`           | Challenge ACME        |
| Consumer Information System | Security Server | `8080`, `8443` | Consumo de serviços   |
| Admin                       | Security Server | `4000`         | UI/API administrativa |

---
### Loopback
Portas internas usadas por componentes locais:

| Componente                      |  Porta | Protocolo | Nota                            |
| ------------------------------- | -----: | --------- | ------------------------------- |
| PostgreSQL                      | `5432` | `tcp`     | Porta padrão PostgreSQL         |
| Operational Monitoring daemon   | `2080` | `tcp`     | Monitoramento operacional       |
| Environmental Monitoring daemon | `2552` | `tcp`     | Monitoramento ambiental         |
| Signer                          | `5559` | `tcp`     | Signer admin port               |
| Signer                          | `5560` | `tcp`     | Signer gRPC port                |
| Proxy                           | `5566` | `tcp`     | Proxy admin port                |
| Proxy                           | `5567` | `tcp`     | Proxy gRPC port                 |
| Configuration Client            | `5675` | `tcp`     | Configuration Client admin port |
| Audit Log                       |  `514` | `udp`     | Logs de auditoria               |

> [!attention]- [[Ponto de atenção]]
> As portas de loopback devem permanecer acessíveis apenas localmente. Não faz sentido expor essas portas externamente.

---
## Interfaces

### Message Protocol

O **X-Road Message Protocol** é usado pelos sistemas de informação para se comunicarem com o Security Server.
O X-Road suporta:
- Message Protocol for SOAP;

- Message Protocol for REST.

Ambos são síncronos e funcionam no modelo request-response.

---
### Service Metadata Protocol
O **Service Metadata Protocol** permite que sistemas clientes descubram informações sobre a instância X-Road.
Ele pode ser usado para encontrar:
- Membros;

- Serviços;

- WSDL;

- OpenAPI 3.

Existem duas versões:
- SOAP;

- REST.

A versão SOAP retorna serviços SOAP.
A versão REST retorna serviços REST.

---
### Downloading Signed Documents
O serviço de download de documentos assinados permite baixar containers ASiC do message log.
Esses containers podem ser verificados offline usando:
```text

asicverifier

```

---
### Operational Monitoring Protocol
Permite que sistemas externos coletem informações operacionais do Security Server.
É síncrono e iniciado pelo sistema externo de monitoramento.

---
### Environmental Monitoring Protocol
Permite coletar informações ambientais do Security Server, como sistema operacional, memória, disco, processos e pacotes.

---
## Deployment models
### Local Database
O modelo mais simples é um único Security Server com banco local.
É normalmente suficiente para:
- Desenvolvimento;

- Testes;

- Ambientes simples.

> [!note]- Resumo
> Para estudo e laboratório, local database costuma ser suficiente.

---
### Remote Database
Também é possível usar banco remoto com o Security Server.
Esse modelo pode ser útil quando se deseja externalizar o estado do banco, especialmente em ambientes de desenvolvimento em nuvem. O Security Server suporta bancos PostgreSQL em provedores como:
- AWS RDS;

- Azure Database for PostgreSQL

---
### High Availability Setup
O X-Road possui um mecanismo interno de seleção de Security Server baseado em **Fastest Wins**.
Quando um mesmo serviço está registrado em múltiplos Security Servers, o cliente escolhe o servidor que responde mais rápido ao estabelecimento de conexão TCP.
Se o Security Server mais rápido parar de responder, o cliente muda para o próximo.
Limitação importante:
- Isso melhora disponibilidade;

- Mas não garante balanceamento de carga uniforme.

---
### Load Balancing Setup
Em produção com alta demanda, pode ser usado um load balancer externo na frente de um cluster de Security Servers.
Nesse modelo:

- O LB distribui requisições entre os nós;

- O health check identifica nós indisponíveis;

- Todos os nós compartilham a mesma identidade;

- O cluster parece um único Security Server para os clientes.

> [!attention]- [[Ponto de atenção]]
> O load balancer deve usar SSL passthrough. A terminação SSL precisa acontecer no Security Server. Terminar SSL no LB causa erro no Security Server cliente.

---
### Tabela de uso

| Deployment              | Dev | Prod |
| ----------------------- | --: | ---: |
| Local database          | `x` |      |
| Remote database         | `x` |      |
| High Availability Setup |     |  `x` |
| Load Balancing Setup    |     |  `x` |

---
## Resisting failure
### Ideias iniciais
O X-Road é altamente resiliente porque usa arquitetura distribuída.
As mensagens são trocadas diretamente entre Security Servers, sem um intermediário central acessando os dados.
A falha de um Security Server afeta apenas os serviços disponíveis naquele Security Server.

---
### Central Server
O Central Server é crítico porque distribui a configuração global.
Porém, o Security Server mantém uma cópia local da configuração global e continua funcionando enquanto essa cópia for válida.
Padrão citado:
- Atualização da configuração global: a cada 60 segundos;

- Validade padrão: 10 minutos;

- O Central Server pode ficar indisponível temporariamente sem derrubar tudo.

> [!attention]- [[Ponto de atenção]]
> Quando a configuração global expira, o processamento de mensagens para. O Security Server não faz fila nem reenvia mensagens automaticamente.

---
### OCSP responder service
O OCSP é usado para verificar a validade dos certificados.
O Security Server baixa e cacheia respostas OCSP periodicamente.
Enquanto tiver respostas OCSP válidas em cache, ele pode continuar operando mesmo que o OCSP responder fique indisponível por um tempo.
Parâmetros importantes:
- `ocspFetchInterval`;

- `ocspFreshnessSeconds`;

- `verifyNextUpdate`.

---
### Time-stamping service
O TSA certifica que determinados dados existiam em um momento específico.
Por padrão, o X-Road usa **batch time-stamping**, agrupando mensagens para reduzir carga.
Pontos importantes:
- Mensagens são time-stamped em lote;

- Se o TSA falha, o Security Server pode continuar por um período aceitável;

- Após atingir o limite de falha, o processamento para;

- Quando o TSA volta, mensagens pendentes são carimbadas.

> [!note]- [[Nota mental]]
> Batch time-stamping reduz muito a carga. Time-stamping síncrono é mais rigoroso, mas aumenta carga e tempo de processamento.

---
### Resumo
A resiliência do ecossistema depende de configurações como:

- Validade da configuração global;

- Intervalo de atualização da configuração;

- Validade das respostas OCSP;

- Intervalo de busca OCSP;

- Período aceitável de falha no time-stamping;

- Uso de múltiplas TSAs;

- Cluster do Central Server;

- Cache local no Security Server.

---
# Data exchange

## Introduction
### Ideias iniciais e entendimento:
Os **X-Road Message Protocols** e o **X-Road Transport Protocol** formam o núcleo da troca de dados no X-Road.
Eles trabalham em camadas diferentes:
- Message Protocols: comunicação entre Information System e Security Server;

- Transport Protocol: comunicação entre Security Servers.

Fluxo geral:
```text

Service Consumer Information System

        ↓

Consumer Security Server

        ↓

X-Road Transport Protocol

        ↓

Provider Security Server

        ↓

Service Provider Information System

```

---
### SOAP e REST
Os serviços devem ser consumidos em sua implementação nativa:
- SOAP como SOAP;

- REST como REST.

O Security Server **não faz conversão automática** entre SOAP e REST.
Caso seja necessário converter, pode ser usado:
```text

REST Adapter Service

```
Mas ele suporta apenas um conjunto limitado de casos.

---
## Transport protocol
### Ideias iniciais
O **X-Road Message Transport Protocol** é usado pelos Security Servers para trocar requisições e respostas.
Ele é:
- Síncrono;

- RPC style;

- Baseado em HTTPS;

- Baseado em TLS mútuo com certificados.

As mensagens SOAP/REST recebidas são encapsuladas em mensagens **MIME multipart**, junto com dados como:
- Assinaturas;

- Respostas OCSP;

- Dados de validação.

---
### Camadas do protocolo
O transport protocol possui duas camadas:
- **Transport Layer**.

- **Application Layer**.

A Transport Layer usa HTTP sobre TLS mutuamente autenticado.
A Application Layer usa mensagens X-Road em MIME multipart trafegando sobre HTTPS.

---
### TLS Authentication
Processo simplificado:
1. Uma requisição chega ao Security Server cliente.

2. O Security Server cliente identifica o Security Server provedor.

3. O Security Server cliente inicia TLS handshake com o provedor na porta `5500`.

4. O cliente recebe a cadeia de certificados do provedor.

5. Verifica respostas OCSP em cache.

6. Se não houver cache, baixa OCSP do provedor.

7. Valida se o certificado foi emitido por uma CA aprovada.

8. Se a validação passar, envia a mensagem.

9. Se falhar, retorna SOAP Fault ao sistema cliente.

10. O provedor também valida o certificado do cliente usando a configuração global.

---
### Download de OCSP
O provedor deve responder requisições HTTP GET na porta:

```text

5577

```

Essa porta é usada para baixar respostas OCSP relacionadas aos certificados.

---
### X-Road Transport Message
As mensagens de transporte são codificadas como:

```text

multipart/mixed

```

Para SOAP, o content-type original é repassado usando:
```text

x-original-content-type

```

Para REST, são usadas partes específicas:

```text

application/x-road-rest-request

application/x-road-rest-response

```

---
## Message protocol for REST
### Ideias iniciais
No contexto do X-Road, REST não é tratado como um protocolo fechado como SOAP.
REST é um estilo arquitetural.
No X-Road, suportar REST significa permitir consumo e fornecimento de APIs REST-style via Security Server.

---
### Content-types suportados
O suporte REST não é limitado a JSON ou XML.
O Security Server não impõe restrições ao tipo de conteúdo do payload.
O conteúdo é definido pelo header:

```text

Content-Type

```

O payload é trafegado como está, sem modificação, conversão ou validação pelo Security Server.

---
### Non-repudiation
Para mensagens REST, os seguintes itens entram na assinatura digital e nos logs:

- Payload;

- Parâmetros de URL;

- Headers HTTP.

Isso permite não repúdio também em mensagens REST.

---
### Consumindo REST
Para consumir REST via X-Road, o serviço chamado é definido na URL.
O cliente X-Road é definido no header:
```http

X-Road-Client: PLAYGROUND/COM/1234567-8/TestClient

```
Exemplo:
```bash

curl -X GET \

-H 'X-Road-Client: PLAYGROUND/COM/1234567-8/TestClient' \

-H 'X-Road-Id: 123' \

-i -k 'https://testcomss01.playground.x-road.systems/r1/PLAYGROUND/GOV/8765432-1/TestService/XRoadStatistics/instances'

```

---
### Fornecendo REST
Serviços REST existentes podem ser publicados praticamente como estão.
Em geral, basta:
- Adicionar a base URL da API;

- Definir direitos de acesso no Security Server;

- Opcionalmente fornecer uma descrição OpenAPI 3.

O Security Server adiciona os headers X-Road necessários na resposta.

---
### OpenAPI 3

Ao publicar uma API REST, é possível informar:

- Base URL;

- URL de uma descrição OpenAPI 3.

Formatos aceitos:

- JSON;

- YAML.

Benefícios:

- Outros membros podem consultar via `getOpenAPI`;

- Endpoints aparecem na interface do Security Server;

- Direitos de acesso podem ser gerenciados por endpoint.

---
### Controle de acesso

Para APIs REST, o acesso pode ser definido em dois níveis:

- Nível da API;

- Nível do endpoint.

No nível do endpoint, o controle usa:

- Método HTTP;

- Path.

> [!attention]- [[Ponto de atenção]]
> O Security Server só suporta permissões de liberação. Não existe “allow all e deny one endpoint”.

---
### Autenticação machine-to-machine

A implementação REST suporta TLS mútuo entre Security Server e consumidor/provedor REST.

JWT entre Security Server e Information System não é suportado como mecanismo do X-Road.

Mas JWT pode ser usado no nível da aplicação, entre cliente e provedor, porque os headers HTTP são repassados.

---
## Message protocol for SOAP

### Ideias iniciais

O **X-Road Message Protocol for SOAP** define como consumidores e provedores se comunicam com o Security Server usando SOAP.

Ele é baseado no SOAP Profile 1.1, com limitações e requisitos específicos:

- Apenas operações síncronas request-response;

- Headers SOAP obrigatórios;

- Corpo no estilo document/literal;

- Suporte a anexos MIME;

- Descrição WSDL 1.1.

---
### Adapter Service

Em SOAP, é comum usar um **Adapter Service** entre o Security Server e o sistema SOAP.

Ele converte mensagens de entrada e saída para o formato esperado pelo X-Road.

---
### Message body

O corpo da mensagem deve usar:

```text

Document/Literal-Wrapped

```

Se a requisição chama `foo`, a resposta deve seguir a lógica:

```text

foo

fooResponse

```

O nome do wrapper da requisição deve corresponder ao `serviceCode` do header `service`.

---
### Attachments

Se a mensagem tiver anexos, ela deve ser uma mensagem multipart MIME.

Regras:

- A requisição SOAP deve ser a primeira parte;

- Os anexos vêm em partes separadas;

- O header `Content-Transfer-Encoding` da parte SOAP deve ser `8bit`;

- MIME headers são preservados pelo Security Server;

- MTOM também é suportado.

---
### WSDL

Serviços SOAP são descritos com:

```text

WSDL 1.1

```

O X-Road suporta serviços versionados.

Uma nova versão deve ser criada quando houver mudanças técnicas menores, como:

- Reestruturação da descrição;

- Renomeação de tipos;

- Alteração de campos;

- Mudanças no XML Schema.

Se mudar a semântica do serviço ou o conteúdo dos dados, o correto é criar um novo serviço com novo código.

---
## Identifying entities

### Entidades principais

As entidades mais importantes no X-Road são:

- Member;

- Subsystem;

- Service.

---
### Member

Um **Member** é uma organização participante autorizada a trocar dados via X-Road.

Formato:

```text

MEMBER:[X-Road instance]/[member class]/[member code]

```

Exemplo:

```text

MEMBER:EE/BUSINESS/123456789

```

---
### Subsystem

Um **Subsystem** representa parte do sistema de informação de um membro.

Formato:

```text

SUBSYSTEM:[subsystem owner]/[subsystem code]

```

Exemplo:

```text

SUBSYSTEM:EE/BUSINESS/123456789/highsecurity

```

---
### Service

Um **Service** é uma API SOAP/REST disponibilizada por um subsistema.

Formato:

```text

SERVICE:[service provider]/[service code]/[service version]

```

Exemplo:

```text

SERVICE:EE/BUSINESS/123456789/highsecurity/getSecureData

```

---
### Identificadores

Os identificadores são hierárquicos e globalmente únicos.

Podem conter:

- Código da instância;

- Classe do membro;

- Código do membro;

- Código do subsistema;

- Código do serviço;

- Versão do serviço;

- Código do Security Server.

---
### Restrições de caracteres

Não devem ser usados:

```text

:

;

/

\

%

```

Também devem ser evitados:

- Caracteres de controle;

- Espaços de largura zero;

- U+0000—U+001F;

- U+007F—U+009F;

- U+200B;

- U+FEFF.

---
## Understanding error messages

### Ideias iniciais

O primeiro passo para resolver erros no X-Road é identificar onde o erro foi gerado.

Componentes no fluxo:

1. Service Consumer;

2. Consumer-side Security Server, ou client proxy;

3. Provider-side Security Server, ou server proxy;

4. Service Provider.

---
### Estrutura de erro

Erros gerados pelo Security Server possuem estrutura fixa.

| REST | SOAP | Descrição |
|---|---|---|
| `type` | `faultCode` | Código e origem do erro |
| `message` | `faultString` | Descrição do erro |
| `detail` | `faultDetail` | ID para buscar no log |

---
### Origem do erro

Se o erro começa com:

```text

Server.ClientProxy

```

Ele veio do Security Server consumidor.

Se começa com:

```text

Server.ServerProxy

```

Ele veio do Security Server provedor.

Logs importantes:

```text

/var/log/xroad/proxy.log

```

---
### Exemplo REST

```json

{

  "type": "Server.ClientProxy.SslAuthenticationFailed",

  "message": "Client (SUBSYSTEM:PLAYGROUND/COM/1234567-8/TestClient) specifies HTTPS but did not supply TLS certificate",

  "detail": "2ea02d93-e0b8-4e2b-8c6e-fac20f53a3e3"

}

```

---
### Header X-Road-Error

Quando uma resposta REST contém erro gerado por Security Server, o header abaixo é incluído:

```text

X-Road-Error

```

---
# Installation and configuration

## Installation

### Plataformas suportadas

As plataformas Linux oficialmente suportadas para o Security Server são:

- Ubuntu;

- Red Hat Enterprise Linux, RHEL.

Também é suportado rodar o Security Server em containers.

---
### Instalação em cluster

Para um Security Server cluster com load balancer externo, existe guia específico de instalação.

Em setup clusterizado, se o operational monitoring for usado, o daemon de operational monitoring deve ser instalado em um host separado e compartilhado pelos nós do cluster.

---
### Pacotes

Os pacotes de instalação são publicados pelo **Nordic Institute for Interoperability Solutions (NIIS)**.

Os pacotes para Linux são assinados pela NIIS.

Ao entrar em um ambiente X-Road existente, a autoridade governante do ambiente pode fornecer um mirror/repositório específico que deve ser usado.

---
## System services

### Serviços principais

Os serviços de sistema mais importantes do Security Server são:

| Serviço | Finalidade | Log |
|---|---|---|
| `xroad-addon-messagelog` | Arquivamento e limpeza do message log | `/var/log/xroad/messagelog-archiver.log` |
| `xroad-confclient` | Download da configuração global | `/var/log/xroad/configuration_client.log` |
| `xroad-proxy` | Processamento de mensagens | `/var/log/xroad/proxy.log` |
| `xroad-signer` | Gerenciamento de chaves | `/var/log/xroad/signer.log` |
| `xroad-proxy-ui-api` | UI administrativa e REST API | `/var/log/xroad/proxy_ui_api.log` |
| `xroad-monitor` | Environmental monitoring | `/var/log/xroad/monitor.log` |
| `xroad-opmonitor` | Operational monitoring | `/var/log/xroad/op-monitor.log` |

Logs adicionais do proxy:

```text

/var/log/xroad/clientproxy_access.log

/var/log/xroad/serverproxy_access.log

```

---
## Memory allocations

### Proxy

A alocação recomendada de memória para o Proxy depende da RAM do host.

| RAM no host | PROXY_PARAMS |
|---:|---|
| 4 GB | `-Xms200m -Xmx512m` |
| 8 GB | `-Xms512m -Xmx2g` |
| 16 GB | `-Xms2g -Xmx8g` |
| 32 GB | `-Xms2g -Xmx16g` |

---
### Signer

A alocação recomendada para o Signer também depende da RAM disponível.

| RAM no host | SIGNER_PARAMS |
|---:|---|
| 4 GB | `-Xms50m -Xmx100m` |
| 8 GB | `-Xms50m -Xmx150m` |
| 16 GB | `-Xms50m -Xmx200m` |
| 32 GB | `-Xms50m -Xmx200m` |

---
## Configuration

### Visão geral

A configuração inicial do Security Server envolve alguns passos fundamentais.

Antes de concluir esses passos, o Security Server ainda não está pronto para trocar dados.

---
### Initial configuration

Informações adicionadas na configuração inicial:

- Arquivo anchor da configuração global;

- Classe do membro dono do Security Server;

- Código do membro dono;

- Código do Security Server;

- PIN do software token.

> [!attention]- [[Ponto de atenção]]
> O PIN protege as chaves no software token. Se o PIN for perdido, não será mais possível usar ou recuperar as chaves privadas armazenadas nele.

---
### Código do Security Server

Depois que a configuração for concluída:

- É possível alterar o dono do Security Server;

- Não é possível alterar o código do Security Server.

---
### Time-stamping, keys e CSRs

Depois da configuração inicial:

- Configurar serviços de time-stamping;

- Criar sign key;

- Gerar CSR de assinatura;

- Criar authentication key;

- Gerar CSR de autenticação;

- Enviar CSRs para a CA.

A sign key pode ficar em:

- Soft token;

- Security token;

- HSM.

A authentication key fica no soft token.

---
### Import certificates

Após a CA fornecer os certificados:

- Importar o sign certificate;

- Importar e ativar o authentication certificate;

- Enviar o pedido de registro do authentication certificate.

Esse pedido precisa ser aprovado pela autoridade governante do X-Road.

Depois da aprovação, o Security Server fica pronto para trocar dados.

---
## Autologin

### Ideias iniciais

O **Autologin** é um utilitário que insere automaticamente o PIN, eliminando a necessidade de digitação manual.

Ele pode prevenir interrupções causadas por ausência do PIN.

Quando o host do Security Server é iniciado ou reiniciado, o Security Server não fica operacional até que o PIN seja informado.

---
### Quando o PIN é necessário

O PIN é necessário quando:

- O servidor host do Security Server é reiniciado;

- O Security Server precisa acessar as chaves protegidas no token.

O PIN não é necessário quando apenas os serviços X-Road são reiniciados.

> [!attention]- [[Ponto de atenção]]
> Autologin melhora disponibilidade, mas também aumenta a responsabilidade sobre como o PIN é armazenado e protegido.

---
# Operations

## User management

### Ideias iniciais

O gerenciamento de usuários do Security Server é baseado em **roles**.

Um usuário pode ter múltiplas roles.

Vários usuários podem estar na mesma role.

Cada role possui um grupo de sistema correspondente, criado na instalação.

---
### Autenticação

O Security Server usa **Linux-PAM** para autenticação de usuários.

Por padrão, é usado:

```text

pam_unix

```

---
### Roles suportadas

| Role | Grupo | Responsabilidade |
|---|---|---|
| Security Officer | `xroad-security-officer` | Política de segurança, chaves e certificados |
| Registration Officer | `xroad-registration-officer` | Registro e remoção de clientes |
| Service Administrator | `xroad-service-administrator` | Dados e direitos de acesso dos serviços |
| System Administrator | `xroad-system-administrator` | Instalação, configuração e manutenção |
| Security Server Observer | `xroad-securityserver-observer` | Acesso somente leitura à UI |

---
## Logs

### Ideias iniciais

O X-Road produz logs em três categorias principais:

- Application logs;

- Business logs;

- Audit logs.

---
### Application logs

Os logs técnicos são gravados por componentes internos como:

- Proxy;

- Signer;

- Confclient;

- Monitor;

- Opmonitor;

- UI API.

Diretório principal:

```text

/var/log/xroad

```

---
### Business logs

O business log do Security Server fica no **message log database**.

Ele contém mensagens processadas pelo Security Server.

Cada mensagem pode ser:

- Assinada;

- Time-stamped;

- Arquivada;

- Verificada depois.

---
### Message log today

O X-Road garante não repúdio usando:

- Time-stamping;

- Assinaturas digitais;

- Message log database;

- ASiC containers.

Opções de message logging:

- Full logging;

- Metadata logging;

- No message logging.

---
#### Full logging

Registra a mensagem completa, incluindo corpo e metadados.

Permite verificação posterior e uso como evidência.

---
#### Metadata logging

Registra apenas metadados, sem o corpo da mensagem.

Nesse modo, os registros servem mais para estatística e relatório, mas perdem valor de evidência porque a assinatura original foi feita sobre a mensagem completa.

---
#### No message logging

Desabilita totalmente o message logging.

Nenhum registro de message log é gerado.

---
### Audit log

O Security Server mantém um audit log para eventos gerados pela UI e Management REST API quando o estado ou configuração do sistema muda.

Local padrão:

```text

/var/log/xroad/audit.log

```

O audit log registra ações com sucesso e falha.

> [!attention]- [[Ponto de atenção]]
> Mudanças feitas fora da UI ou da Management REST API podem não entrar no audit log, como instalação, upgrade, criação de usuário e alteração direta de arquivos de configuração.

---
### Archiving logs

Os logs do Security Server são locais, inclusive em ambientes clusterizados.

Eles não são replicados automaticamente entre nós do cluster.

Recomendação:

- Enviar logs para armazenamento externo;

- Usar logging centralizado;

- Planejar retenção;

- Monitorar uso de disco;

- Arquivar message logs adequadamente.

---
### Estimativa de disco do message log

Fórmula aproximada:

```text

3.6kB + N * (21kB + R + A) = S

```

Onde:

- `N` = número de requisições por minuto;

- `R` = tamanho do payload da requisição em kB;

- `A` = tamanho do payload da resposta em kB;

- `S` = uso de disco por minuto.

Exemplo:

```text

3.6 kB + 100 * (21 kB + 4 kB + 8 kB) = 3303.6 kB/min

```

Isso dá aproximadamente:

- 4.8 GB por dia;

- 143 GB por mês;

- Mais espaço adicional para arquivos arquivados.

> [!attention]- [[Ponto de atenção]]
> Message log pode crescer muito rápido em ambientes de alto tráfego. Disco cheio pode causar parada de serviço.

---
## Backups

### Ideias iniciais

O Security Server possui funcionalidade de backup e restore.

O backup inclui:

- Cópia do banco `serverconf`;

- Arquivos de configuração modificáveis pelo usuário;

- Chaves e certificados;

- Credenciais de banco;

- Certificado TLS interno;

- Certificado UI/API;

- Auth key/certificate;

- Sign keys/certificates armazenados no soft token.

---
### O que não entra no backup

Por padrão, ficam fora:

- Message log database;

- Operational monitoring database;

- Message log archive files.

> [!attention]- Ponto de atenção
> Chaves de criptografia do message log só entram no backup se estiverem armazenadas em `/etc/xroad`.

---
### Como executar backup

Backups podem ser feitos por:

- Security Server UI;

- Management REST API;

- Linha de comando.

Backups são sempre assinados e podem ser criptografados.

O GnuPG é usado para assinatura e criptografia.

---
### Backup automático

Por padrão:

- Backup automático ocorre uma vez por dia;

- Backups com mais de 30 dias são removidos;

- Política pode ser ajustada em `/etc/cron.d/xroad-proxy`.

Agendamento pode ser ajustado em:

```text

/etc/xroad/conf.d/local.ini

```

Exemplo:

```ini

[configuration-client]

proxy-configuration-backup-cron=* 15 3 * * ?

```

---
### Recomendação

É fortemente recomendado transferir backups para um local externo e seguro.

Assim, os backups continuam disponíveis mesmo que o host do Security Server seja perdido.

---
## Monitoring

### Ideias iniciais

O monitoramento do X-Road é dividido em:

- Environmental Monitoring;

- Operational Monitoring.

---
### Environmental Monitoring

Fornece dados do ambiente do Security Server, como:

- Sistema operacional;

- Memória;

- Espaço em disco;

- CPU load;

- Processos em execução;

- Pacotes instalados;

- Versão do X-Road;

- Informações de certificados.

---
### Operational Monitoring

Coleta dados sobre trocas de requisição entre Security Servers.

Inclui:

- IDs do cliente e serviço;

- Atributos de headers;

- Timestamps da requisição e resposta;

- Tamanho da mensagem;

- Tempo de processamento;

- Sucesso/falha.

---
### Controle de acesso ao monitoramento

Consultas de monitoramento são permitidas para:

- Cliente dono do Security Server;

- Cliente central de monitoramento;

- Cliente comum consultando seus próprios registros operacionais.

Tentativas não autorizadas retornam:

```text

AccessDenied

```

---
## System parameters

### Ideias iniciais

Os system parameters definem características do sistema, como:

- Portas;

- Timeouts;

- Caminhos em disco;

- Limites;

- Comportamentos de componentes.

Eles ficam em arquivos INI.

---
### local.ini

Para sobrescrever valores padrão, criar ou editar:

```text

/etc/xroad/conf.d/local.ini

```

Formato:

```ini

[ServerComponent]

SystemParameterName1=Value1

SystemParameterName2=Value2

```

Exemplo:

```ini

[proxy]

client-http-port=1234

server-listen-port=20000

```

Após alterar parâmetros, é necessário reiniciar o serviço afetado.

> [!attention]- Ponto de atenção
> Os valores não são sempre validados. Configurar porta inválida ou valor incorreto pode derrubar o componente.

---
## Communication with client information systems

### Ideias iniciais

O Security Server pode se comunicar com sistemas cliente usando:

- HTTP;

- HTTPS;

- HTTPS NOAUTH.

A recomendação geral é usar **HTTPS**.

---
### HTTP

Deve ser usado apenas se o sistema de informação e o Security Server estiverem em um segmento privado e isolado.

O servidor do sistema de informação não deve permitir login interativo.

---
### HTTPS

É o padrão para novos clientes.

Deve ser usado quando não há um segmento de rede separado.

Exige certificados TLS internos dos sistemas de informação enviados ao Security Server.

---
### HTTPS NOAUTH

Usado quando se deseja que o Security Server não verifique o certificado TLS do sistema de informação.

A comunicação ainda usa HTTPS, mas sem validação do certificado do cliente.

---
### Comportamento especial

Se o método configurado for HTTP, mas o sistema se conectar usando HTTPS, a conexão é aceita, mas o certificado TLS interno não é verificado.

Isso se comporta como HTTPS NOAUTH.

> [!attention]- Ponto de atenção
> Em Security Server compartilhado por vários membros, é extremamente importante manter todos os clientes como HTTPS para evitar uso indevido.

---
## Management REST API

### Ideias iniciais

O Security Server possui uma REST API para fazer as mesmas operações de configuração e manutenção disponíveis na UI web.

A autenticação é feita com API keys.

As API keys possuem roles associadas.

Se uma API key for perdida, ela pode ser revogada.

---
### Limites de tamanho

A REST API possui limites de tamanho de requisição.

| Tipo | Limite |
|---|---:|
| Upload de arquivo binário | 10 MB |
| Outras requisições | 50 KB |

Se o limite for excedido, retorna:

```text

413 Payload Too Large

```

---
### Rate limit

A API também possui rate limit por IP.

| Limite | Valor |
|---|---:|
| Por minuto | 600 requests |
| Por segundo | 20 requests |

Se o limite for excedido, retorna:

```text

429 Too Many Requests

```

---
### Parâmetros ajustáveis

Os limites podem ser alterados em parâmetros do componente `proxy-ui-api`:

```text

request-sizelimit-regular

request-sizelimit-binary-upload

rate-limit-requests-per-second

rate-limit-requests-per-minute

```

Exemplo:

```ini

[proxy-ui-api]

rate-limit-requests-per-second=100

request-sizelimit-binary-upload=1MB

```

---
## Federation

### Ideias iniciais

A **Federation** permite que Security Servers de duas instâncias X-Road diferentes troquem mensagens entre si.

A federação é configurada no nível do Central Server.

Depois disso, os Security Servers podem optar por participar da federação.

Por padrão, federation fica desabilitada.

---
### allowed-federations

A federação pode ser permitida para:

- Todas as instâncias oferecidas pelo Central Server;

- Uma lista específica de instâncias;

- Nenhuma instância.

Configuração em:

```text

/etc/xroad/conf.d/local.ini

```

Parâmetro:

```ini

[configuration-client]

allowed-federations=none

```

Para permitir uma lista específica:

```ini

[configuration-client]

allowed-federations=INSTANCE1,INSTANCE2

```

Para mudanças surtirem efeito, reiniciar na ordem:

```bash

systemctl restart xroad-confclient

systemctl restart xroad-proxy

```

---
# X-Road Security Server - Cheat Sheet

%%Essa sessão está escondida porque contém pontos rápidos para revisão. Use por conta e risco.%%

> [!note]- X-Road Security Server Cheat Sheet
>
> ## Tracking de perguntas e respostas
>
> > **Qual é o processo mais importante do Security Server?**
>
> - `xroad-proxy`
>
> > **Qual banco é usado pelo Security Server?**
>
> - PostgreSQL
>
> > **Qual porta padrão da UI/API administrativa?**
>
> - `4000`
>
> > **Quais portas são usadas para troca entre Security Servers e OCSP?**
>
> - `5500/tcp`
> - `5577/tcp`
>
> > **O Security Server faz conversão automática SOAP-REST?**
>
> - Não
>
> > **Qual componente gerencia chaves e certificados?**
>
> - `xroad-signer`
>
> > **Qual componente baixa a configuração global?**
>
> - `xroad-confclient`
>
> > **Qual arquivo sobrescreve system parameters?**
>
> - `/etc/xroad/conf.d/local.ini`
>
> > **Quais plataformas Linux são oficialmente suportadas?**
>
> - Ubuntu
> - Red Hat Enterprise Linux
>
> > **Quais são os dois tipos de monitoramento?**
>
> - Environmental Monitoring
> - Operational Monitoring
>
> > **Qual header REST identifica o cliente X-Road?**
>
> - `X-Road-Client`
>
> > **Qual header REST indica erro gerado pelo Security Server?**
>
> - `X-Road-Error`
>
> > **Qual ferramenta verifica containers ASiC?**
>
> - `asicverifier`
>
> > **O que acontece se a configuração global local expira?**
>
> - O Security Server para de processar mensagens
>
> > **Backups incluem message log database?**
>
> - Não
>
> > **Onde fica o audit log por padrão?**
>
> - `/var/log/xroad/audit.log`
>
> > **Qual status HTTP aparece quando a Management REST API recebe payload grande demais?**
>
> - `413 Payload Too Large`
>
> > **Qual status HTTP aparece quando a Management REST API excede rate limit?**
>
> - `429 Too Many Requests`
>
> > **What is the main function of the autologin utility?**
>
> - Enter the PIN code automatically after a restart
>
> > **O que o Autologin evita na operação do Security Server?**
>
> - Que o administrador precise desbloquear manualmente o token/chave depois de uma reinicialização do host.
>
> > **Which items are excluded from Security Server backups?**
>
> - Message log database
> - Operational monitoring database
> - Message log archive files
>
> > **Security Server backups incluem o banco `serverconf`?**
>
> - Sim. O backup de configuração inclui o banco `serverconf`.
>
> > **Qual é a natureza do backup do Security Server?**
>
> - É um backup de configuração, não um backup completo de logs e monitoramento.
>
> > **What is the maximum number of different members that can be associated with one Security Server?**
>
> - Unlimited
> - Não confundir com o `Security Server owner`: a pergunta fala de members/clients associados ao Security Server.
>
> > **Qual arquivo é usado para sobrescrever valores padrão dos system parameters?**
>
> - `/etc/xroad/conf.d/local.ini`
>
> > **What is the default state of message log archive encryption and grouping?**
>
> - Disabled
>
> > **Por padrão, message log archive encryption e grouping vêm habilitados?**
>
> - Não. Ambos vêm desabilitados por padrão.
>
> > **What is the easiest way to monitor the health of the Security Server?**
>
> - Use the health check API
>
> > **Para que serve a Health Check API no Security Server?**
>
> - Verificar via HTTP se o Security Server está saudável para operação e monitoramento por load balancer ou ferramenta externa.

---
# Referências

## Introduction

#### X-Road Documentation

https://docs.x-road.global/

#### Terms and Abbreviations

https://docs.x-road.global/terms_x-road_docs.html

#### X-Road Architecture

https://docs.x-road.global/Architecture/arc-g_x-road_arhitecture.html

#### Security Server Architecture

https://docs.x-road.global/Architecture/arc-ss_x-road_security_server_architecture.html

## Architecture

#### Technologies Used in X-Road

https://docs.x-road.global/Architecture/arc-tec_x-road_technologies.html

#### Operational Monitoring Daemon Architecture

https://docs.x-road.global/OperationalMonitoring/Architecture/arc-opmond_x-road_operational_monitoring_daemon_architecture_Y-1096-1.html

#### Environmental Monitoring Architecture

https://docs.x-road.global/EnvironmentalMonitoring/Monitoring-architecture.html

#### Security Server Configuration Data Model

https://docs.x-road.global/DataModels/dm-ss_x-road_security_server_configuration_data_model.html

## Data Exchange

#### Message Transport Protocol

https://docs.x-road.global/Protocols/pr-messtransp_x-road_message_transport_protocol.html

#### Message Protocol for REST

https://docs.x-road.global/Protocols/pr-rest_x-road_message_protocol_for_rest.html

#### Message Protocol for SOAP

https://docs.x-road.global/Protocols/pr-mess_x-road_message_protocol.html

#### Service Metadata Protocol

https://docs.x-road.global/Protocols/pr-meta_x-road_service_metadata_protocol.html

#### Service Metadata Protocol for REST

https://docs.x-road.global/Protocols/pr-mrest_x-road_service_metadata_protocol_for_rest.html

#### Protocol for Downloading Configuration

https://docs.x-road.global/Protocols/pr-gconf_x-road_protocol_for_downloading_configuration.html

#### Protocol for Management Services

https://docs.x-road.global/Protocols/pr-mserv_x-road_protocol_for_management_services.html

#### Signed Document Download and Verification Manual

https://docs.x-road.global/Manuals/ug-sigdoc_x-road_signed_document_download_and_verification_manual.html

## Installation and Configuration

#### Security Server Installation Guide for Ubuntu

https://docs.x-road.global/Manuals/ig-ss_x-road_v6_security_server_installation_guide.html

#### Security Server Installation Guide for Red Hat Enterprise Linux

https://docs.x-road.global/Manuals/ig-ss_x-road_v6_security_server_installation_guide_for_rhel.html

#### Security Server User Guide

https://docs.x-road.global/Manuals/ug-ss_x-road_6_security_server_user_guide.html

#### System Parameters User Guide

https://docs.x-road.global/Manuals/ug-syspar_x-road_v6_system_parameters.html

#### Signer Console User Guide

https://docs.x-road.global/Manuals/ug-sc_x-road_signer-console_user_guide.html

#### Security Hardening Guidelines

https://docs.x-road.global/Manuals/ug-sec_x_road_security_hardening.html

## Operations

#### Security Server Management Use Cases

https://docs.x-road.global/UseCases/uc-ss_x-road_use_case_model_for_security_server_management_1.4_Y-883-4.html

#### Audit Log Events

https://docs.x-road.global/Architecture/spec-al_x-road_audit_log_events.html

#### Message Log Data Model

https://docs.x-road.global/DataModels/dm-ml_x-road_message_log_data_model.html

#### Operational Monitoring Protocol

https://docs.x-road.global/OperationalMonitoring/Protocols/pr-opmon_x-road_operational_monitoring_protocol_Y-1096-2.html

#### Operational Monitoring System Parameters

https://docs.x-road.global/OperationalMonitoring/Manuals/ug-opmonsyspar_x-road_operational_monitoring_system_parameters_Y-1099-1.html

#### Environmental Monitoring Messages

https://docs.x-road.global/EnvironmentalMonitoring/Monitoring-messages.html

## High Availability and Load Balancing

#### External Load Balancer Installation Guide

https://docs.x-road.global/Manuals/LoadBalancing/ig-xlb_x-road_external_load_balancer_installation_guide.html
