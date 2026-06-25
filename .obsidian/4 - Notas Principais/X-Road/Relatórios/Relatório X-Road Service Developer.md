2026-06-22 01:15

Status: #adult

Tags: [[x-road]] [[programação]] [[relatórios]] [[estudo]]
- - -
# Decorrer
## [[#Introduction|Introduction]]
### Ideias iniciais e entendimento:
Hoje dia 21/06 eu comecei de fato meu estudos sobre o x-road e como funciona fora do que eu ouvi sobre do mesmo do Lezier e do Bruno.
A ferramenta a primeira olhada é um api gateway que utiliza de uma camada de interoperabilidade que permite que diferentes organizações e sistemas conversem entre si de forma segura, auditável e padronizada.
Terminei o curso inicial que é basicamente um estudo legal (lei) e técnico introdutório da ferramenta e uma visão geral do escopo de [[#Architecture Architecture|arquitetura]] 
Oque é reforçado no estudo base da prática é que a [[x-road]] é uma ferramente que por exemplo:
Existe uma [[api|API]] na lógica para uma API. A mesma vai ser obrigada a passar por um server de segurança, que irá contatar o serviço X-road e la dentro o exchange de dados vai ser feito.
Mais ou menos assim: 

`API-A → Security Layer Server → X-Road Service ← Security Layer Server ← API-B`
                                  `↓`
                            `Data Exchange`

Supondo que a **API-A** queira buscar um cliente na **API-B**, a API real da API-B teria seu endpoint teorico em:

```
GET /v1/customers/123
```

Mas via **X-Road**, a API-A chamaria o **Security Server**, não a API-B diretamente:

```
GET http://security-server-a:8080/r1/BR/GOV/API_B/clientes-service/v1/customers/123X-Road-Client: BR/PRIVATE/API_A/backend
```

No padrão REST do X-Road, a URL usa `/r1/` seguido do identificador do serviço destino, e o header `X-Road-Client` identifica quem está fazendo a chamada. O caminho depois do `serviceCode` é repassado para o serviço real.

- - - 
## [[#Architecture|Architecture]]

##### Ilustrações e Bases:
Tecnicamente, o ecossistema X-Road é composto por:
Serviços Centrais ([[X Road - Central Services]]),
Servidores de Segurança ([[X Road - Security Server]]), 
Sistemas de Informação ([[X Road - Information System]]), 
Autoridade de Registro de Tempo (TSAs) ([[X Road - Time-Stamping Authority (TSA)]]), 
Autoridade de Certificação (ACs) ([[X Road - Certification Authority (CA)]]).

--- 
### Intro
#### Main Components e Interfaces do sistema X-Road:
	Tudo que está cinza não é parte do X-Road Core

![[Pasted image 20260622173144.png]]

#### Central Server

O **Central Server** gerencia os membros do X-Road e os Security Servers registrados na instância.

Ele também mantém a política de segurança da instância, incluindo:

- Autoridades certificadoras confiáveis;
- Autoridades de carimbo de tempo confiáveis;
- Parâmetros de segurança, como tempo máximo de validade de respostas OCSP.

Essas informações são distribuídas aos Security Servers por HTTP e formam a chamada **configuração global**.

Além disso, o Central Server permite executar tarefas administrativas, como adicionar ou remover clientes de um Security Server.

#### Security Server

O **Security Server** é o principal componente operacional do X-Road.

Ele atua como intermediário entre o sistema cliente e o sistema provedor de serviço, cuidando dos aspectos de segurança da comunicação.

Suas principais responsabilidades são:

- Gerenciar chaves de assinatura e autenticação;
- Enviar mensagens por canais seguros;
- Criar provas digitais das mensagens;
- Aplicar assinatura digital;
- Realizar time-stamping;
- Registrar logs das requisições e respostas;
- Suportar comunicação via SOAP e REST.

Para os sistemas conectados, o Security Server funciona de forma transparente, ou seja, as aplicações se comunicam com ele sem precisar lidar diretamente com toda a complexidade de segurança do X-Road.

Um único Security Server pode atender várias organizações, funcionando em modelo **multi-tenant**.

![[Pasted image 20260622173538.png]]

#### Information System

É a aplicação que consome ou fornece serviços via X-Road.
No lado cliente, o sistema usa o Security Server como ponto de entrada para acessar serviços disponíveis na rede X-Road. No lado provedor, o sistema implementa um serviço SOAP ou REST e o disponibiliza por meio do Security Server.
Serviços SOAP devem possuir uma descrição WSDL. Serviços REST podem possuir uma especificação OpenAPI v3.

#### Time-Stamping Authority

Emite carimbos de tempo que comprovam que determinado dado existia em um momento específico.
O X-Road utiliza **batch time-stamping**, ou seja, agrupa mensagens para reduzir a carga sobre o serviço de carimbo de tempo. Com isso, a carga do TSA depende mais da quantidade de Security Servers do que da quantidade total de mensagens trocadas.

#### Configuration Proxy

Atua como uma fonte adicional de configuração global.
Ele baixa a configuração, armazena localmente e a disponibiliza para outros componentes.
Sua função principal é aumentar a disponibilidade do sistema e reduzir a carga sobre o Central Server.    // [[Nota Mental]] -> Esse cara é importante para o balacing do system

#### Operational Monitoring

Coleta e armazena dados operacionais do Security Server. Esses dados podem ser utilizados por sistemas externos de monitoramento para acompanhar o funcionamento da infraestrutura.

#### Environmental Monitoring

Coleta informações sobre o ambiente onde o Security Server está executando.
Isso inclui dados relacionados ao sistema operacional, recursos e condições do ambiente de execução. Essas informações também podem ser disponibilizadas para ferramentas externas de monitoramento.

#### Fluxo Geral

```
Information System Cliente
        ↓
Security Server Cliente
        ↓
	 X-Road
        ↓
Security Server Provedor
        ↓
Information System Provedor
```

Durante esse processo, o Security Server garante autenticação, assinatura, logs, time-stamping e validação dos certificados separa a lógica de negócio da lógica de segurança. A aplicação não precisa implementar diretamente assinatura digital, validação de certificados, logs seguros ou carimbo de tempo. Essas responsabilidades ficam concentradas no Security Server.

---
### Networking
É fortemente recomendado proteger o [[X Road - Security Server]] contra acessos indesejados utilizando um **firewall**, que pode ser baseado em hardware ou software.
Esse firewall pode ser aplicado tanto para conexões de entrada quanto para conexões de saída, dependendo dos requisitos de segurança do ambiente onde o Security Server está instalado.
A recomendação principal é permitir conexões de entrada apenas em portas específicas e somente a partir de fontes explicitamente definidas, utilizando filtragem por IP.
Uma configuração incorreta do firewall pode deixar o Security Server vulnerável a ataques e explorações.

Para que o operador do X-Road consiga monitorar o ecossistema, gerar estatísticas e oferecer suporte aos membros, é necessário permitir conexões vindas do **Monitoring Security Server** para o Security Server nas portas:
```
5500/tcp
5577/tcp
```
Essas portas são importantes para o monitoramento e comunicação entre Security Servers.
![[Pasted image 20260622180557.png]]
#### Portas, Protocolos e outras conexões

![[X Road - Conexões de Saída|Conexões de Saída]]

![[X Road - Conexões de Entrada|Conexões de Entrada]]
##### ![[X Road - LoopBack|LoopBack]]
-------------------------------------------------------------------------------------------------------------

As portas externas devem ser liberadas apenas quando forem realmente necessárias e, sempre que possível, restritas por IP. As portas de loopback são utilizadas apenas pelos componentes internos do Security Server e devem permanecer acessíveis somente localmente.

---
### Keys and Certificates
A partir da versão **7.0.0**, o [[X Road - Security Server]] passou a utilizar múltiplas chaves de criptografia, essas chaves tem duas categorias principais:
- Chaves usadas para proteger dados em trânsito;
- Chaves usadas para proteger dados em repouso.

#### Proteção de Dados em Trânsito

Dados em trânsito são os dados que estão sendo transmitidos entre sistemas, Security Servers, APIs ou interfaces administrativas.

O Security Server possui cinco tipos principais de chaves e certificados para proteger esse tipo de comunicação.

---
##### 1. Authentication Key and Certificate

A **authentication key** e o **authentication certificate** certificam a autenticidade de um Security Server.
Eles são usados para autenticação nas conexões entre Security Servers.
As chaves de autenticação são sempre armazenadas no **soft token**.

---
##### 2. Sign Key and Certificate
A **sign key** e o **sign certificate** certificam a autenticidade de um membro do X-Road.
Eles são usados para assinar mensagens e verificar a integridade das mensagens mediadas pelo Security Server.
As chaves de assinatura podem ser armazenadas em:
- Soft token;
- Security token;
- Hardware Security Module, ou HSM.
---
##### 3. Internal TLS Certificate
O **internal TLS certificate** é utilizado nas conexões entre o Security Server e um sistema de informação.
Esse certificado pode atuar tanto como certificado de cliente quanto como certificado de servidor, dependendo do papel exercido pelo Security Server e pelo sistema de informação.

---
##### 4. UI/API TLS Certificate
O **UI/API TLS certificate** é usado no acesso à interface administrativa do Security Server e à API REST de gerenciamento.
Por padrão, essa interface roda na porta:
```
4000
```
A chave TLS e o certificado autoassinado são gerados automaticamente durante o processo de instalação do Security Server.

---
##### 5. API Keys
As **API keys** são usadas para autenticar chamadas feitas para a API REST de gerenciamento do Security Server.
Essas chaves são associadas a papéis, ou **roles**, que definem quais permissões aquela API key possui.

---
#### Proteção de Dados em Repouso

Dados em repouso são dados armazenados, como backups, logs, arquivos de mensagens e dados salvos em banco.
O Security Server possui quatro tipos principais de chaves para proteger esses dados.

---
##### 1. Internal GPG Key

A **internal GPG key** é usada em operações relacionadas a backup e arquivos de log.
A chave privada é usada para:
- Assinar arquivos de backup;
- Assinar arquivos de log arquivados quando a criptografia de arquivos de log está habilitada;
- Descriptografar arquivos de backup durante a restauração.

A chave pública é usada para:
- Criptografar arquivos de backup quando a criptografia de backup está habilitada;
- Criptografar arquivos de log arquivados quando a criptografia está habilitada e não há chave específica por membro;
- Verificar a integridade de um arquivo de backup durante a restauração.

---

##### 2. Backup/Restore GPG Keys
As **backup/restore GPG keys** são chaves públicas adicionais usadas para criptografar arquivos de backup quando a criptografia de backup está habilitada.

---
##### 3. Database Encryption Key
A **database encryption key** é usada para criptografar o corpo das mensagens armazenadas no banco de dados de logs.
Essa chave é utilizada quando a criptografia do banco de dados está habilitada.

---
##### 4. Message Log Archive Encryption Keys
As **message log archive encryption keys** são chaves públicas adicionais usadas para criptografar arquivos de log arquivados.
Essas chaves são específicas por membro.
Elas são usadas quando a criptografia e o agrupamento de arquivos de log estão habilitados, permitindo criptografar arquivos pertencentes a um membro específico.

Em resumo geral cada chave aqui possui uma responsabilidade específica dentro da arquitetura do [[x-road|X Road]] e são usadas algumas em casos especificos e outras em casos obrigatórios.

---
### Communication with information systems

#### Service Consumers
O [[X Road - Security Server]] pode se comunicar com sistemas de informação usando diferentes protocolos, dependendo se o sistema atua como **consumidor** ou **provedor** de serviço.
- HTTP;
- HTTPS;
- HTTPS NOAUTH.
A recomendação geral é sempre utilizar **HTTPS**, pois ele protege a comunicação usando criptografia.
##### HTTP
O protocolo **HTTP** deve ser usado apenas quando o sistema de informação e o Security Server se comunicam em um segmento de rede privado, isolado ou ==para testes==. Esse segmento não deve possuir outros computadores conectados. Além disso, o servidor do sistema de informação não deve permitir login interativo.

---
##### HTTPS
O protocolo **HTTPS** é o padrão para novos clientes e é o mais recomendado.
Ele deve ser usado quando não é possível garantir um segmento de rede separado entre o sistema de informação e o Security Server.
Nesse caso, a comunicação é protegida por métodos criptográficos contra interceptação e espionagem.
Antes de usar HTTPS, é necessário criar certificados TLS internos para os sistemas de informação e enviá-los para o Security Server.

---
##### HTTPS NOAUTH
O protocolo **HTTPS NOAUTH** deve ser usado quando se deseja que o Security Server não verifique o certificado TLS do sistema de informação.
Ou seja, a comunicação ainda usa HTTPS, mas sem autenticação do certificado TLS interno do cliente.

---
##### Comportamento Especial
Se o método selecionado for **HTTP**, mas o sistema de informação se conectar ao Security Server usando HTTPS, a conexão será aceita, porém, o certificado TLS interno do cliente não será verificado. Esse comportamento é semelhante ao uso de HTTPS NOAUTH.

---

##### Configuração Padrão

Por padrão, o tipo de conexão dos clientes do Security Server é definido como **HTTPS**.
Isso ajuda a evitar o uso não autorizado dos clientes.
Também é recomendado manter o tipo de conexão do dono do Security Server como HTTPS, evitando que outros clientes façam requisições de monitoramento operacional como se fossem o proprietário do Security Server.
Em ambientes onde um Security Server é compartilhado por vários membros, é extremamente importante configurar todos os clientes como HTTPS para evitar uso indevido.

- - -
---
#### Service Providers

Os **service providers** são sistemas de informação que fornecem serviços via X-Road.
Nesse caso, o método de conexão é definido pelo protocolo usado na URL do endpoint do serviço.
A URL pode usar HTTP ou HTTPS.

---
##### HTTP
O protocolo **HTTP** é usado quando a URL do serviço ou adaptador começa com:

```
http://
```

Nesse caso, a comunicação não utiliza TLS.

---
##### HTTPS

O protocolo **HTTPS** é usado quando a URL do serviço ou adaptador começa com:
```
https://
```
E a opção **Verify TLS certificate** está marcada.
Antes de usar HTTPS, os certificados TLS internos dos sistemas de informação precisam ser criados e enviados ao Security Server.

---
##### HTTPS NO AUTH
O modo **HTTPS NO AUTH** é usado quando a URL do serviço ou adaptador começa com:
```
https://
```
Mas a opção **Verify TLS certificate** não está marcada.
Nesse caso, a conexão usa HTTPS, porém o certificado TLS do serviço não é verificado.

---
##### Verify TLS Certificate

A opção **Verify TLS certificate** afeta a verificação do certificado TLS em dois cenários:

- Na comunicação entre o Security Server e o endpoint do serviço;
- Na comunicação entre o Security Server e a URL de descrição do serviço, quando os metaservices `getWsdl` ou `getOpenAPI` são usados.
####  Em resumo geral do módulo
para [[#Service Consumers|consumidores de serviço]], o Security Server pode aceitar conexões HTTP, HTTPS ou HTTPS NOAUTH.
Para [[#Service Providers|provedores de serviço]], o protocolo é definido pela URL do endpoint do serviço.

###### **Nota final: More information on configuring the connection type of services is available https://docs.x-road.global/Manuals/ug-ss_x-road_6_security_server_user_guide.html#92-communication-with-service-provider-information-systems== 
---


### Interfaces
O [[x-road]] possui diferentes protocolos usados para comunicação entre sistemas de informação, [[X Road - Security Server]] e sistemas externos de monitoramento.

Esses protocolos são responsáveis por permitir a troca de mensagens, consulta de metadados, download de documentos assinados e coleta de informações operacionais e ambientais.

---
#### Message Protocol

O **X-Road Message Protocol** é utilizado pelos sistemas de informação clientes e provedores para se comunicarem com o X-Road Security Server.

O X-Road suporta dois protocolos de mensagem:

- Message Protocol for SOAP;
- Message Protocol for REST.

Ambos os protocolos são **síncronos**, ou seja, funcionam no modelo de requisição e resposta.

Eles podem ser iniciados pelo sistema de informação cliente ou pelo Security Server do provedor de serviço.

---

#### Service Metadata Protocol

O **X-Road Service Metadata Protocol** é usado pelos sistemas de informação clientes para coletar informações sobre a instância X-Road.
Esse protocolo permite encontrar:
- Membros do X-Road;
- Serviços oferecidos pelos membros;
- Descrições WSDL;
- Descrições OpenAPI 3.
O protocolo possui duas versões:
- Uma versão para SOAP;
- Uma versão para REST.
A versão SOAP fornece informações apenas sobre serviços SOAP.
A versão REST fornece informações apenas sobre serviços REST.
Portanto, caso seja necessário coletar informações sobre todos os serviços SOAP e REST disponíveis, as duas versões dos metaservices devem ser chamadas.
Esse protocolo também é síncrono, segue o estilo RPC e é iniciado pelo sistema de informação cliente.

---
#### Downloading Signed Documents

O serviço de **download de documentos assinados** pode ser usado pelos sistemas de informação para baixar containers ASiC assinados a partir do log de mensagens do Security Server.
Esses containers podem ser transferidos para terceiros e verificados offline.
Além disso, o serviço oferece um método para baixar a configuração global necessária para verificar os containers.
A verificação dos containers pode ser feita com a ferramenta de linha de comando:

```
asicverifier
```

A ferramenta `asicverifier` acompanha o Security Server e pode ser usada para:
- Verificar containers assinados;
- Extrair arquivos assinados dos containers.
Esse protocolo é síncrono, segue o estilo RPC e é iniciado pelo sistema de informação.

---
#### Operational Monitoring Protocol
O **X-Road Operational Monitoring Protocol** pode ser usado por sistemas externos de monitoramento para coletar informações operacionais do Security Server.
Esse protocolo é síncrono, segue o estilo RPC e é iniciado pelo sistema externo de monitoramento.

---
#### Environmental Monitoring Protocol
O **Environmental Monitoring Protocol** pode ser usado por sistemas externos de monitoramento para coletar informações ambientais sobre o Security Server.
Essas informações estão relacionadas ao ambiente onde o Security Server está executando.

---

### Monitoring

O monitoramento no [[X-Road]] é dividido conceitualmente em duas partes:
- **Environmental Monitoring**
- **Operational Monitoring**
Esses dois tipos de monitoramento permitem acompanhar tanto o ambiente onde o [[X Road - Security Server]] está executando quanto as trocas de mensagens realizadas entre Security Servers.

---
#### Environmental Monitoring
O **Environmental Monitoring** fornece detalhes sobre o ambiente do Security Server.
Ele pode incluir informações como:
- Sistema operacional;
- Memória;
- Espaço em disco;
- Carga de CPU;
- Processos em execução;
- Pacotes instalados;
- Versão do X-Road;
- Informações de certificados.
Também é possível limitar o conjunto de dados retornado para o cliente central de monitoramento. Quando limitado, o conjunto de dados inclui apenas:
- Informações de certificados;
- Sistema operacional;
- Versão do X-Road.
---
#### Operational Monitoring
O **Operational Monitoring** coleta dados sobre a troca de requisições entre Security Servers. Esses dados incluem, mas não se limitam a:
- Identificadores do cliente e do serviço;
- Atributos da mensagem lidos do cabeçalho;
- Timestamps da requisição e da resposta;
- Tamanho da mensagem;
- Tempo de processamento.
Esse tipo de monitoramento está mais relacionado ao acompanhamento das comunicações realizadas dentro da infraestrutura X-Road.
---
#### Controle de Acesso ao Monitoramento
As consultas de monitoramento ambiental e operacional são permitidas para:
- O cliente que é dono do Security Server;
- Um cliente central de monitoramento, caso esteja configurado.
Além disso, um cliente comum pode consultar seus próprios registros de monitoramento operacional, ou seja, ele só pode acessar registros associados às próprias requisições. O cliente central de monitoramento é configurado pela interface administrativa do Central Server. Tentativas de consulta feitas por clientes não autorizados resultam em uma resposta de sistema:
```
AccessDenied
```
---
#### Em resumo do Módulo
O **Environmental Monitoring** observa o ambiente do Security Server, como sistema operacional, memória, CPU, pacotes e versão do X-Road.
O **Operational Monitoring** observa as trocas de mensagens entre Security Servers, incluindo informações de cliente, serviço, timestamps, tamanho da mensagem e tempo de processamento.
O acesso aos dados de monitoramento é restrito ao dono do Security Server, ao cliente central de monitoramento e, no caso operacional, ao próprio cliente dono dos registros.

---
## [[#Data Exchange|Data Exchange]]
### Intro
Os **X-Road Message Protocols** e o **X-Road Transport Protocol** formam o núcleo da troca de dados no [[X-Road]].
Eles trabalham em camadas diferentes da comunicação:
- Os **Message Protocols** são usados pelos sistemas de informação para se comunicar com o [[X Road - Security Server]];
- O **Transport Protocol** é usado pelos Security Servers para trocar requisições e respostas entre si.
#### Message Protocols

Os **Message Protocols** são utilizados pelos sistemas de informação clientes e provedores para comunicação com o X-Road Security Server. O X-Road oferece dois tipos de protocolo de mensagem:
- Message Protocol for SOAP;
- Message Protocol for REST.

Esses protocolos são usados conforme a implementação nativa do serviço, ou seja, um serviço SOAP deve ser consumido como SOAP, e um serviço REST deve ser consumido como REST.

---
#### Message Transport Protocol
O **Message Transport Protocol** é utilizado pelos Security Servers para trocar requisições de serviço e respostas de serviço, esse protocolo é baseado em:
- HTTPS;
- Autenticação TLS mútua baseada em certificados.
Isso significa que os Security Servers se autenticam entre si usando certificados, garantindo uma comunicação segura entre as partes.
##### ![[Pasted image 20260622234149.png]]
---
#### SOAP e REST no X-Road
Os serviços devem ser consumidos usando suas implementações nativas:
- SOAP;
- REST.
Se um provedor quiser disponibilizar o mesmo serviço em SOAP e REST, ele precisa implementar as duas versões o Security Server não faz conversão automática entre SOAP e REST.
###### ![[Pasted image 20260622234232.png]]

---
#### Conversão SOAP-REST
O Security Server não fornece conversão automática entre SOAP e REST caso essa conversão automática seja necessária, pode ser usada a extensão:
```
REST Adapter Service
```
O **REST Adapter Service** é um componente pronto que funciona como um conversor REST-SOAP compatível com X-Road.
Porém, ele suporta apenas um conjunto limitado de casos de uso.

---
#### Fluxo Geral da Troca de Dados
De forma resumida, o fluxo de comunicação entre consumidor e provedor acontece assim:
```
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
Nesse processo, os sistemas de informação usam os protocolos de mensagem SOAP ou REST para conversar com seus Security Servers.
Já os Security Servers usam o Transport Protocol baseado em HTTPS com autenticação TLS mútua para trocar mensagens entre si.
##### ![[Pasted image 20260622234338.png]]

---
#### Resumo
Os **Message Protocols** e o **Transport Protocol** são a base da troca de dados no X-Road.
Os Message Protocols conectam os sistemas de informação ao Security Server usando SOAP ou REST.
O Transport Protocol conecta os Security Servers entre si usando HTTPS e autenticação TLS mútua baseada em certificados.
O Security Server não converte automaticamente serviços SOAP para REST ou REST para SOAP. Para isso, é necessário implementar as duas versões do serviço ou usar uma extensão específica, como o REST Adapter Service.

---
### Transport protocol
O **X-Road Message Transport Protocol** é usado pelo [[X Road - Security Server]] para trocar requisições e respostas de serviço.
Esse protocolo é síncrono, no estilo **RPC**, e é iniciado pelo Security Server do cliente do serviço.
Ele é baseado em **HTTPS** e utiliza autenticação TLS mútua baseada em certificados.
As mensagens SOAP ou REST recebidas do sistema cliente ou provedor são encapsuladas em mensagens **MIME multipart**, junto com dados adicionais de segurança, como:
- Assinaturas digitais;
- Respostas OCSP;
- Dados relacionados à validação da comunicação.
Esse protocolo, junto com os protocolos de mensagem SOAP e REST do X-Road, forma o núcleo da troca de dados do X-Road. Se os componentes envolvidos não estiverem disponíveis, a troca de dados não acontece. Por isso, a arquitetura do X-Road permite melhorar a disponibilidade usando redundância.
---
#### Basics
O protocolo de transporte é dividido em duas camadas:
- **Transport Layer**
- **Application Layer**
A **Transport Layer** usa HTTP sobre TLS com autenticação mútua.
A **Application Layer** usa mensagens X-Road codificadas em MIME multipart, que são trocadas sobre HTTPS.

###### ![[Pasted image 20260622235951.png]]
---
#### Fluxo Geral
O Security Server do cliente recebe uma requisição do sistema de informação cliente.
Depois disso, ele encapsula essa requisição em uma mensagem de transporte X-Road e a envia para o Security Server do provedor.
O Security Server do provedor recebe essa mensagem, extrai a requisição original e a encaminha para o sistema de informação provedor. Após o sistema provedor responder, o Security Server do provedor encapsula a resposta em uma nova mensagem de transporte X-Road e envia de volta ao Security Server do cliente.
Por fim, o Security Server do cliente entrega a resposta ao sistema de informação cliente.

```
Client Information System
        ↓
Client Security Server
        ↓
X-Road Message Transport Protocol
        ↓
Provider Security Server
        ↓
Provider Information System
```

---

#### Transport Layer
A camada de transporte é responsável pela comunicação segura entre os Security Servers.
Ela usa:
- HTTPS;
- TLS com autenticação mútua;
- Certificados de autenticação;
- Validação por cadeia de certificados;
- Respostas OCSP.
![[Pasted image 20260623000017.png]]
---
#### TLS Authentication
Os Security Servers usam certificados de autenticação para iniciar uma troca de mensagens autenticada mutuamente. Cada certificado de autenticação precisa estar registrado no **Central Server**. Além disso, a autoridade certificadora que emitiu os certificados precisa estar aprovada no Central Server.
Durante a autenticação TLS, a cadeia de certificados deve chegar até uma autoridade certificadora confiável registrada na configuração global.

---
#### Processo de Estabelecimento da Conexão Segura
O processo básico ocorre assim:
1. Uma requisição X-Road chega ao Security Server do cliente.
2. O Security Server do cliente processa a requisição e identifica o Security Server do provedor de serviço.
3. O Security Server do cliente inicia o handshake TLS com o Security Server do provedor, usando a porta padrão:
```
5500
```
4. Durante o handshake TLS, o Security Server do cliente recebe a cadeia de certificados de autenticação do Security Server do provedor.
5. O Security Server do cliente verifica se possui respostas OCSP em cache para os certificados recebidos.
6. Se as respostas OCSP não estiverem em cache, o Security Server do cliente baixa essas respostas a partir do Security Server do provedor e armazena localmente.
7. O Security Server do cliente verifica se o certificado de autenticação do provedor foi emitido por uma autoridade certificadora aprovada.
8. A cadeia de certificação e as respostas OCSP são validadas.
9. Se a validação for bem-sucedida, a mensagem de transporte X-Road é enviada ao Security Server do provedor.
10. Se a validação falhar, o Security Server do cliente retorna uma mensagem **SOAP Fault** ao sistema de informação cliente.
11. Ao receber a mensagem, o Security Server do provedor também valida a cadeia de certificados do Security Server do cliente usando a configuração global.

---
#### Download de Respostas OCSP
Cada Security Server interage apenas com a CA que emitiu seus próprios certificados. Por isso, as respostas OCSP dos certificados são transferidas junto com os próprios certificados. Os Security Servers armazenam em cache as respostas OCSP de seus certificados e atualizam esse cache periodicamente.
Antes de enviar a requisição, o Security Server do cliente precisa verificar o certificado de autenticação do Security Server do provedor.
Para isso, ele baixa as respostas OCSP do Security Server do provedor usando um canal separado via HTTP.
O Security Server do provedor deve responder a requisições HTTP GET na porta padrão:
```
5577
```
Nessa requisição, o Security Server do cliente informa para quais certificados deseja obter respostas OCSP.

---
#### Application Layer
A camada de aplicação trata da estrutura e integridade das mensagens transportadas entre Security Servers. A integridade da mensagem transmitida é garantida pela assinatura da mensagem de transporte X-Road dentro do Security Server.
Essa assinatura pode ser:
- Assinatura regular;
- Assinatura em lote, ou **batch signature**.
As batch signatures devem ser usadas em mensagens que possuem anexos.
Elas também podem ser úteis quando a chave de assinatura está em um dispositivo seguro mais lento, permitindo assinar várias mensagens simultaneamente.

---
#### Streaming de Mensagens
O X-Road Message Transport Protocol foi projetado para permitir streaming do conteúdo das mensagens, como anexos, entre Security Servers. A assinatura pode ser calculada depois que partes anteriores da mensagem, como anexos, já foram transferidas para a outra parte.
Por causa disso, existem restrições na verificação da assinatura. O conteúdo da mensagem precisa ser armazenado em cache no Security Server antes que a assinatura seja verificada.
Isso é necessário porque o resultado da verificação define se a mensagem é válida ou não.
O Security Server não deve encaminhar uma mensagem inválida para a outra parte.

---
#### X-Road Transport Message
As mensagens de transporte X-Road são codificadas como mensagens **MIME multipart**, usando o content-type:
```
multipart/mixed
```
A mensagem de transporte encapsula a mensagem SOAP ou REST recebida pelo Security Server.
Ela também pode encapsular uma mensagem **SOAP Fault**, quando ocorre erro antes do processamento da requisição no Security Server do provedor.
Nesse caso, o content-type usado é:
```
text/xml
```
##### ![[Pasted image 20260623000047.png]]
---
#### Tratamento de Mensagens SOAP
Para mensagens SOAP, o content-type original da requisição ou resposta é enviado entre os Security Servers usando o cabeçalho HTTP:
```
x-original-content-type
```
Esse valor é usado para encaminhar a mensagem ao sistema de informação correto.
Outros cabeçalhos HTTP enviados pelos Security Servers não são preservados.
Já os cabeçalhos [[MIME]] dentro da mensagem multipart são preservados.

---
#### Tratamento de Mensagens REST
Para mensagens REST, o content-type da requisição ou resposta é enviado usando partes específicas da mensagem:
```
application/x-road-rest-request
application/x-road-rest-response
```
Essas partes contêm o chamado **REST header part**.
O REST header part inclui:
- Linha da requisição HTTP, no caso de requests;
- Linha de status HTTP, no caso de responses;
- Cabeçalhos HTTP, em ambos os casos.
Além do cabeçalho `Content-Type`, outros cabeçalhos também podem ser preservados, mas o comportamento varia. Alguns cabeçalhos são adicionados ou substituídos pelo Security Server, como:
```
x-road-request-id
```
Alguns cabeçalhos são removidos, como:
```
User-Agent
```
Outros cabeçalhos são repassados sem alteração, como:
```
X-Powered-By
```

---
#### Resumo
O **X-Road Message Transport Protocol** é o protocolo usado para comunicação entre Security Servers. Ele usa HTTPS com autenticação TLS mútua baseada em certificados. As mensagens SOAP e REST são encapsuladas em mensagens MIME multipart, junto com dados de segurança como assinaturas e respostas OCSP.
A camada de transporte garante a comunicação segura entre Security Servers.
A camada de aplicação garante a estrutura, assinatura, integridade e encapsulamento das mensagens. Esse protocolo é essencial para a troca de dados no X-Road, pois sem ele os Security Servers não conseguem trocar requisições e respostas de serviço.

### Identifying entities
As entidades mais importantes no [[X-Road]] são:
- **Member**
- **Subsystem**
- **Service**
Essas entidades representam os participantes, partes dos sistemas de informação e serviços disponíveis para troca de dados dentro do X-Road.
#### Member
Um **Member** é um participante do X-Road, normalmente uma organização, autorizado a trocar dados e mensagens pela infraestrutura X-Road. Um membro pode estar associado a vários [[X Road - Security Server]]. Ao mesmo tempo, um Security Server pode estar associado a no máximo dois membros simultaneamente.

---
#### Subsystem
Um **Subsystem** representa uma parte do sistema de informação de um membro do X-Road. Os membros precisam declarar partes de seus sistemas como subsistemas para poder consumir ou fornecer serviços via X-Road. Os direitos de acesso de cada subsistema são independentes. Isso significa que permissões concedidas a um subsistema não afetam os outros subsistemas do mesmo membro.
Da mesma forma, os serviços fornecidos por um subsistema são independentes dos serviços fornecidos por outros subsistemas do mesmo membro. Para assinar mensagens enviadas por um subsistema, é usado o certificado de assinatura do membro que gerencia esse subsistema. Um membro pode associar vários subsistemas diferentes a um Security Server. Um subsistema também pode estar associado a vários Security Servers. Além disso, vários subsistemas gerenciados por membros diferentes podem estar associados ao mesmo Security Server.

---
#### Service
Um **Service** é um serviço web ou API baseado em SOAP ou REST, fornecido por um subsistema X-Road. Esse serviço pode ser consumido por outros subsistemas do X-Road.
Os direitos de acesso a serviços podem ser concedidos para:
- Um subsistema;
- Um grupo global de direitos de acesso;
- Um grupo local de direitos de acesso.
---
#### Grupos Globais
Os **global access rights groups** são criados pela autoridade governante do X-Road.
Quando um grupo global recebe permissão de acesso, essa permissão se estende a todos os membros do grupo.

---
#### Grupos Locais
Os **local access rights groups** podem ser criados no próprio Security Server. Eles servem para simplificar o gerenciamento de permissões. Quando um grupo local recebe permissão de acesso, essa permissão se aplica a todos os membros desse grupo.

---
#### Direitos de Acesso em SOAP e REST
Para serviços **SOAP**, os direitos de acesso são definidos no nível do serviço.
Para serviços **REST**, os direitos de acesso podem ser definidos em dois níveis:
- Nível da API;
- Nível do endpoint.

---
#### Identifiers
Entidades importantes no X-Road possuem identificadores globalmente únicos. Esses identificadores são formados por uma sequência hierárquica de códigos.
Esses códigos podem incluir:
- Identificador da instância;
- Classe do membro;
- Código do membro;
- Código do subsistema;
- Código do serviço;
- Versão do serviço;
- Código do serviço central;
- Código do Security Server.
Todos os identificadores começam com o código da instância X-Road.
Normalmente, esse código corresponde ao código ISO do país que opera a instância, podendo ter um sufixo indicando o ambiente.
Exemplos:
```
EE
ee-test
```
No exemplo, `EE` representa o ambiente de produção da Estônia, enquanto `ee-test` representa o ambiente de teste.
Os códigos das instâncias X-Road precisam ser globalmente únicos. As outras partes dos identificadores são gerenciadas pela própria instância X-Road.

---
#### Formato dos Identificadores
Ao representar entidades como texto, o formato usado é:
```
T:C1/C2/...
```
Onde:
- `T` representa o tipo da entidade;
- `C1`, `C2` e os demais valores representam os códigos hierárquicos.

---
#### Identificador de Member
O identificador de um membro segue o formato:
```
MEMBER:[X-Road instance]/[member class]/[member code]
```
Ele é composto por:
- Código da instância X-Road;
- Classe do membro;
- Código do membro.
Exemplo:
```
MEMBER:EE/BUSINESS/123456789
```
Esse identificador representa uma organização registrada na Estônia, na classe `BUSINESS`, com o código `123456789`.

---
#### Identificador de Subsystem
O identificador de um subsistema segue o formato:
```
SUBSYSTEM:[subsystem owner]/[subsystem code]
```
Ele é composto pelo identificador do membro dono do subsistema e pelo código do subsistema. O código do subsistema é escolhido pelo próprio membro e deve ser único entre os subsistemas desse membro.
Exemplo:
```
SUBSYSTEM:EE/BUSINESS/123456789/highsecurity
```
Esse identificador representa o subsistema `highsecurity`, pertencente ao membro:
```
MEMBER:EE/BUSINESS/123456789
```
---
#### Identificador de Service
O identificador de um serviço segue o formato:
```
SERVICE:[service provider]/[service code]/[service version]
```
Ele é composto por:
- Identificador do provedor do serviço;
- Código do serviço;
- Versão do serviço.
O provedor pode ser um membro X-Road ou um subsistema.
A versão é opcional e pode ser usada para diferenciar versões tecnicamente incompatíveis do mesmo serviço.
Exemplo:
```
SERVICE:EE/BUSINESS/123456789/highsecurity/getSecureData
```
Esse identificador representa o serviço `getSecureData`, fornecido pelo subsistema `highsecurity`.

---
#### Restrições de Caracteres
O X-Road Message Protocol impõe restrições aos caracteres usados nos valores dos identificadores.

Os seguintes caracteres não devem ser usados nos valores dos identificadores:
```
:
;
/
\
%
```
Também não devem ser usados:
- Caracteres de controle;
- Espaços de largura zero;
- Caracteres entre `U+0000—U+001F`;
- Caracteres entre `U+007F—U+009F`;
- `U+200B`;
- `U+FEFF`.
Isso inclui caracteres como tabulação, quebra de linha e delete.

---
#### Resumo
No X-Road, as principais entidades são **Member**, **Subsystem** e **Service**. O **Member** representa a organização participante. O **Subsystem** representa uma parte do sistema de informação de um membro.
O **Service** representa uma API ou serviço SOAP/REST disponibilizado por um subsistema. Cada entidade possui identificadores hierárquicos e globalmente únicos, começando sempre pelo identificador da instância X-Road. Esses identificadores seguem formatos específicos e possuem restrições de caracteres para garantir padronização e compatibilidade dentro da infraestrutura X-Road.

---
## [[#Developing Services|Developing Services]]
### Message headers 
O **roteamento de mensagens** no [[X-Road]] é baseado em identificadores. Esses identificadores são mapeados pelo X-Road para os endereços físicos de rede onde os serviços estão disponíveis. A ideia principal é que o sistema que envia a requisição e o serviço que será chamado sejam identificados por meio desses identificadores.
Dependendo do protocolo usado, os identificadores são enviados em:
- Headers SOAP, no caso do protocolo SOAP;
- Headers HTTP, no caso do protocolo REST.
Além de identificar quem envia e quem recebe a mensagem, os headers também podem carregar informações adicionais.
---
#### Headers Específicos do X-Road

| SOAP Header                | REST HTTP Header         | Obrigatório | Descrição                                                                                                                                  |
| -------------------------- | ------------------------ | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `client`                   | `X-Road-Client`          | Sim         | Identifica o cliente do serviço, ou seja, a entidade que inicia a chamada. Pode ser um membro ou subsistema.                               |
| `service`                  | `X-Road-Service`         | `* / **`    | Identifica o serviço invocado pela requisição. No REST, o serviço é definido pela URL da requisição e o header aparece apenas na resposta. |
| `centralService`           | -                        | `**`        | Identifica o serviço central invocado pela requisição.                                                                                     |
| `id`                       | `X-Road-Id`              | Sim         | Identificador único da mensagem. Recomenda-se usar UUID. No REST, se estiver ausente, o Security Server consumidor gera o valor.           |
| -                          | `X-Road-Request-Id`      | Não         | Identificador único da requisição, gerado pelo Security Server do lado cliente. O cliente não deve enviar esse header.                     |
| `userId`                   | `X-Road-UserId`          | Não         | Identifica o usuário cuja ação iniciou a requisição. Não deve conter informações sensíveis.                                                |
| `issue`                    | `X-Road-Issue`           | Não         | Identifica a aplicação, caso, processo ou documento relacionado à requisição.                                                              |
| `protocolVersion`          | -                        | Sim         | Versão do protocolo de mensagem SOAP do X-Road. O valor deve ser `4.0`.                                                                    |
| `requestHash`              | `X-Road-Request-Hash`    | Não         | Em respostas, contém o hash Base64 da mensagem SOAP de requisição. É gerado automaticamente pelo Security Server do provedor.              |
| `requestHash/@algorithmId` | -                        | Não         | Identifica o algoritmo usado para calcular o hash do campo `requestHash`.                                                                  |
| `securityServer`           | `X-Road-Security-Server` | Não         | Identifica o Security Server que fornece o serviço chamado. Pode ser usado quando o serviço está disponível em vários Security Servers.    |

---
#### Observações sobre REST
No protocolo REST, quando o sistema cliente envia uma requisição ao Security Server consumidor, o serviço chamado é especificado na URL. Por isso, o header `X-Road-Service` não deve ser incluído na requisição. A resposta contém o header `X-Road-Service`, que é definido pelo Security Server do provedor.
No protocolo REST, o único header X-Road obrigatório na requisição é:
```
X-Road-Client
```
---
#### Observações sobre SOAP
No protocolo SOAP, quando o sistema cliente envia uma requisição ao Security Server consumidor, exatamente um dos campos abaixo deve estar presente:
- `service`
- `centralService`
Se o campo `centralService` for usado, o Security Server resolve o serviço central e preenche automaticamente o campo `service` com o identificador do serviço concreto que implementa esse serviço central. Assim, na requisição enviada ao serviço, os dois campos podem estar presentes, mas o campo `service` sempre estará presente.

---
#### Headers Definidos pelo Usuário
Além dos headers específicos do X-Road, os protocolos SOAP e REST também suportam headers definidos pelo usuário.

No protocolo SOAP, esses headers devem ser definidos como **SOAP headers**.
No protocolo REST, devem ser usados **HTTP headers**.

---
#### HTTP Headers no REST
No protocolo REST, os HTTP headers definidos pelo usuário são repassados ao destinatário sem modificações pelo Security Server. Isso vale tanto para requisições quanto para respostas.
Porém, existem algumas exceções documentadas na seção de headers filtrados do protocolo REST.

---
#### HTTP Headers no SOAP
No protocolo SOAP, apenas alguns headers HTTP são preservados.
Na requisição do cliente, os headers preservados são:
- `content-type`
- `SOAPAction`
Esses headers são armazenados no Security Server e encaminhados ao serviço. Todos os outros headers HTTP da requisição não são preservados pelos Security Servers e não são encaminhados ao serviço.
Na resposta SOAP, o header preservado é:
- `content-type`
Todos os demais headers HTTP da resposta do serviço não são preservados nem encaminhados ao cliente.
---
#### Headers nas Respostas
A resposta também precisa conter alguns headers específicos do X-Road.
A forma como esses headers são adicionados depende do protocolo utilizado.

---
#### Respostas REST
No protocolo REST, o Security Server do provedor adiciona automaticamente os headers HTTP específicos do X-Road na resposta. O serviço provedor não deve definir esses headers manualmente, pois eles serão sobrescritos. Isso significa que serviços REST podem ser publicados no X-Road praticamente como estão, sem alterações obrigatórias nos headers.

---
#### Respostas SOAP
No protocolo SOAP, o serviço precisa copiar todos os campos de header da requisição para a resposta. Essa cópia deve manter:
- A mesma sequência;
- Os mesmos valores;
- O mesmo namespace.
O prefixo XML do namespace não é importante para o Security Server, desde que ele referencie o mesmo namespace usado na requisição. Por isso, antes de publicar um serviço SOAP no X-Road, normalmente é necessário adaptar o serviço para atender aos requisitos de headers. Também podem existir requisitos específicos no corpo da mensagem SOAP.

![[Pasted image 20260623001834.png]]

---
#### Adapter Service
Um **Adapter Service** é um componente usado para converter requisições ou respostas para o formato esperado pelo X-Road Message Protocol for SOAP ou pelo X-Road Message Protocol for REST. Ele pode ser implementado diretamente no serviço ou ficar entre o serviço e o Security Server do provedor.
Existem várias implementações open source disponíveis no GitHub.
Antes de desenvolver um adapter service do zero, é recomendado verificar alternativas já existentes. Também é possível usar uma plataforma de integração ou uma solução de **Enterprise Service Bus** como adapter service.

---
#### Resumo
O roteamento de mensagens no X-Road depende dos identificadores presentes nos headers da mensagem.

No SOAP, esses identificadores são enviados em headers SOAP.
No REST, eles são enviados em HTTP headers ou definidos pela própria URL da requisição.

O REST exige menos adaptação do serviço, pois o Security Server adiciona automaticamente os headers X-Road nas respostas.
Já o SOAP exige mais cuidado, pois o serviço precisa copiar corretamente os headers da requisição para a resposta.

Quando o serviço não atende diretamente ao formato exigido pelo X-Road, pode ser usado um **Adapter Service** para fazer essa conversão.

---
### Message protocol for REST 
##### // <- [[Nota Mental]] "Esse cara é um bom exemplo da aplicação do X-Road" 

No contexto do [[X-Road]], **REST** não é tratado como um protocolo fechado e detalhado como o SOAP.
REST é um estilo arquitetural baseado em boas práticas e diretrizes para construção de APIs.
No X-Road, suportar REST significa permitir o consumo e fornecimento de APIs REST-style por meio da infraestrutura X-Road.

**De forma ampla, isso significa suportar qualquer tipo de conteúdo trafegando sobre HTTP.**

---
#### REST no X-Road
É possível consumir e fornecer serviços REST via X-Road sem a necessidade de um componente adicional de adapter service. As informações específicas do X-Road exigidas pelo [[X Road - Security Server]], como identificador do cliente, identificador do provedor e ID da mensagem, são transmitidas fora do payload da mensagem.
Essas informações são enviadas principalmente por:
- HTTP headers;
- Parâmetros de URL.
Isso permite que serviços REST existentes sejam conectados ao X-Road com poucas alterações ou até mesmo sem alterações.

---
#### Exemplo de Requisição REST
Exemplo de uma requisição REST usando o X-Road Message Protocol for REST:
```
curl -X GET \
-H 'X-Road-Client: PLAYGROUND/COM/1234567-8/TestClient' \
-H 'X-Road-Id: 123' \
-i -k 'https://testcomss01.playground.x-road.systems/r1/PLAYGROUND/GOV/8765432-1/TestService/XRoadStatistics/instances'
```
Nesse exemplo, o header `X-Road-Client` identifica o subsistema cliente que está fazendo a chamada. O header `X-Road-Id` identifica a mensagem.

---
#### Exemplo de Resposta REST
Exemplo de resposta:
```
HTTP/1.1 200 OK
Content-Type: application/json
x-road-id: 123
x-road-client: PLAYGROUND/COM/1234567-8/TestClient
x-road-service: PLAYGROUND/GOV/8765432-1/TestService/XRoadStatistics
x-road-request-id: 6396aea3-6de2-487d-a7d9-c19541b89679
x-road-request-hash: 9+WrURr10FtXvj+GAeQz6HOQEulv9/2MUGxagMLvk5rDo6CuS27p4AqwaxtCDGsKa3SEPGmAZ/21ISFMB2XSYA==
Content-Length: 187

[
  { "instanceIdentifier": "EE" },
  { "instanceIdentifier": "FI-DEV" },
  { "instanceIdentifier": "FI" },
  { "instanceIdentifier": "ee-test" },
  { "instanceIdentifier": "ee-dev" },
  { "instanceIdentifier": "FI-TEST" }
]
```

A resposta contém headers adicionados pelo Security Server, como:
- `x-road-id`;
- `x-road-client`;
- `x-road-service`;
- `x-road-request-id`;
- `x-road-request-hash`.

---
#### Content-Types Suportados
O suporte REST do X-Road não é limitado apenas a JSON ou XML. O Security Server não impõe restrições ao tipo de conteúdo do payload transferido entre consumidor e provedor.
O tipo de conteúdo é definido pelo header HTTP:
```
Content-Type
```
O payload é transferido como está. O Security Server não modifica, converte ou valida o conteúdo processado. O mesmo vale para quase todos os headers HTTP e parâmetros de URL definidos pelo consumidor ou provedor. Eles são repassados entre as partes sem alteração, com exceção dos headers filtrados definidos na especificação do X-Road Message Protocol for REST.

---
#### Non-Repudiation
No X-Road, a não repudiação também se aplica às mensagens REST. Isso significa que os seguintes elementos são incluídos na assinatura digital e nos logs gerados e verificados pelo Security Server:
- Payload da mensagem;
- Parâmetros de URL;
- Headers HTTP.
Assim, o X-Road garante não repudiação para requisições e respostas REST da mesma forma que faz com mensagens SOAP. Também é possível desabilitar o logging do corpo da mensagem REST.
Nesse caso, são excluídos do message log:
- Payload da mensagem REST;
- Parâmetros de URL;
- Headers definidos pelo consumidor ou provedor.

---
#### Consumindo Serviços REST
Consumir serviços REST via X-Road é simples. O serviço chamado é definido na URL da requisição. O subsistema cliente X-Road que envia a requisição é definido em um HTTP header. O header obrigatório principal é:
```
X-Road-Client
```
Outras informações específicas do X-Road são opcionais e também podem ser passadas por headers HTTP, como:
- User ID;
- Issue;
- ID da mensagem.
Outros headers HTTP, parâmetros de path e parâmetros de URL são repassados de ponta a ponta sem alteração.
Na prática, para o cliente, a principal diferença em relação a uma chamada direta é a necessidade de informar o subsistema cliente X-Road no header.

---
#### Fornecendo Serviços REST
Fornecer serviços REST via X-Road também é simples. Serviços REST existentes podem ser publicados no X-Road praticamente como estão.
Em geral, basta:
- Adicionar a base URL da API REST;
- Definir os direitos de acesso na interface do Security Server.

Diferente dos serviços SOAP, o Security Server não exige que informações específicas do X-Road estejam presentes nas respostas retornadas pelos serviços REST.
O próprio Security Server adiciona as informações necessárias aos headers HTTP da resposta enviada ao sistema cliente.

---
#### Descrições de Serviço REST

Ao publicar uma nova API REST no X-Road, é possível escolher entre duas formas:

- Informar a base URL da API;
- Informar a URL de uma descrição OpenAPI 3 da API.

A descrição OpenAPI 3 pode ser fornecida nos formatos:

- JSON;
- YAML.

O uso de OpenAPI 3 é suportado, mas não é obrigatório.

---
##### Benefícios do OpenAPI 3

Ao fornecer uma descrição OpenAPI 3, outros membros do X-Road podem consultar essa descrição usando o metaservice:

```
getOpenAPI
```

Esse funcionamento é semelhante ao uso do `getWsdl` para consultar descrições WSDL de serviços SOAP.

Outro benefício é que o Security Server lê os endpoints definidos na descrição OpenAPI e os exibe na interface administrativa.

Esses endpoints podem ser usados no gerenciamento de direitos de acesso.

---

#### Endpoints Manuais
Também é possível adicionar endpoints manualmente no Security Server. Porém, endpoints criados manualmente não ficam visíveis para outros membros X-Road por meio do metaservice `getOpenAPI`. Mesmo assim, eles podem ser usados no gerenciamento de direitos de acesso.
Endpoints manuais podem ser:
- Atualizados por administradores;
- Excluídos por administradores.
Já endpoints lidos a partir de uma descrição OpenAPI não podem ser editados ou removidos manualmente. Eles devem ser atualizados ou removidos alterando a descrição OpenAPI 3 e depois atualizando essa descrição no Security Server.

A mesma lógica se aplica a serviços SOAP publicados por meio de descrições WSDL.

---
#### Validação de Endpoints
Além do gerenciamento de direitos de acesso, o Security Server não usa as informações dos endpoints para outras validações.
Por exemplo, ele não valida se um endpoint informado na requisição realmente existe dentro da API. Se o sistema cliente tiver permissão suficiente para chamar o endpoint, o Security Server encaminha a requisição para o endpoint especificado sem validações adicionais.

---
#### Gerenciamento de Direitos de Acesso
Para APIs REST, os direitos de acesso podem ser definidos em dois níveis:
- Nível da API REST;
- Nível do endpoint.

Quando os direitos são definidos no nível da API, eles se aplicam a todos os endpoints daquela API. Quando são definidos no nível do endpoint, o controle é mais detalhado. Nesse caso, o acesso é definido pela combinação de:
- Método HTTP;
- Caminho do endpoint.
Também é possível usar wildcards para permitir acesso a um conjunto de endpoints.

---

#### Limitações do Controle de Acesso
Se um cliente possui acesso no nível da API, ele pode acessar todos os endpoints dessa API. Caso o cliente não deva acessar todos os endpoints, as permissões precisam ser configuradas no nível do endpoint. O gerenciamento de acesso do Security Server suporta apenas permissões de liberação. Ele não suporta negação explícita, pu seja, não é possível liberar acesso para toda a API e depois negar acesso a um endpoint específico.

---
#### Sem Conversão Automática SOAP-REST
Os serviços devem ser consumidos em sua implementação nativa:
- SOAP como SOAP;
- REST como REST.
Se um provedor quiser fornecer o mesmo serviço em SOAP e REST, ele precisa implementar as duas versões.
O Security Server não faz conversão automática entre SOAP e REST.
Caso seja necessária uma conversão automática, pode ser usada a extensão:
```
REST Adapter Service
```
O **REST Adapter Service** é um componente pronto que funciona como conversor REST-SOAP compatível com X-Road.
Porém, ele suporta apenas um conjunto limitado de casos de uso.

---
#### Autenticação Machine-to-Machine
A implementação REST suporta autenticação TLS mútua entre o Security Server e o consumidor ou provedor REST. Autenticação baseada em JWT entre o Security Server e um sistema de informação não é suportada. Porém, é possível usar JWT entre o cliente do serviço e o provedor do serviço.
Como o Security Server repassa os headers HTTP entre consumidor e provedor, não há impedimento para implementar autenticação JWT no nível da aplicação.

---

#### Resumo
O X-Road permite consumir e fornecer APIs REST de forma simples, usando HTTP headers e parâmetros de URL para transportar as informações específicas do X-Road.
O Security Server não modifica nem valida o payload REST, apenas encaminha os dados e adiciona as informações necessárias para segurança, roteamento, logging e não repudiação.
Serviços REST existentes podem ser publicados praticamente sem alterações.
O uso de OpenAPI 3 é opcional, mas ajuda na documentação, descoberta de serviços e gerenciamento de direitos de acesso por endpoint.
O X-Road não realiza conversão automática entre SOAP e REST, e a autenticação JWT pode ser usada apenas no nível da aplicação, não entre o Security Server e o sistema de informação.

---
### Message protocol for SOAP
O **X-Road Message Protocol for SOAP** define como consumidores e provedores de serviço se comunicam com o [[X Road - Security Server]] usando SOAP.
Esse protocolo é baseado no **SOAP Profile 1.1**, mas possui algumas limitações e requisitos específicos do X-Road.
Entre eles estão:
- Suporte apenas para operações síncronas de request-response;
- Uso obrigatório de alguns SOAP headers;
- Corpo da mensagem no estilo **document/literal**;
- Estrutura específica para mensagens, anexos e descrições de serviço.
---
#### Adapter Service
Uma abordagem comum é usar um componente adicional chamado **adapter service** entre o Security Server e o cliente ou serviço SOAP. Assim como citado em ´[[#Sem Conversão Automática SOAP-REST]]´
Esse componente implementa o X-Road Message Protocol for SOAP e converte mensagens de entrada e saída para o perfil SOAP esperado pelo X-Road, ou seja, ele funciona como uma camada de adaptação entre o sistema existente e o Security Server.
Caso seja necessária conversão automática entre SOAP e REST, pode ser usada a extensão:
```
REST Adapter Service
```
O **REST Adapter Service** é um componente pronto que fornece um conversor REST-SOAP compatível com X-Road.
Porém, ele suporta apenas um conjunto limitado de casos de uso.

---
#### Exemplo de Requisição SOAP
Exemplo de uma chamada SOAP usando o X-Road Message Protocol for SOAP:
```
curl -X POST \
-d @helloService.xml \
-H "Content-Type: text/xml" \
-k https://testcomss01.playground.x-road.systems
```
Nesse exemplo, o arquivo `helloService.xml` contém a mensagem SOAP enviada ao Security Server.
![[Pasted image 20260623005208.png]]

---
#### Message Body
O corpo da mensagem deve usar a convenção:
```
Document/Literal-Wrapped
```
Segundo essa convenção, tanto a requisição quanto a resposta devem estar envolvidas por um elemento XML. Os nomes dos elementos da requisição e da resposta são relacionados.
Exemplo:
```
foo
fooResponse
```
Além disso, o nome do elemento wrapper da requisição deve corresponder ao campo `serviceCode` presente no header `service`.

---
#### Attachments
Quando a mensagem possui anexos, ela deve ser formatada como uma mensagem **multipart MIME**.
Nesse caso:
- A requisição SOAP deve ser a primeira parte da mensagem;
- Os anexos devem ser partes separadas;
- A mensagem deve seguir a especificação SOAP messages with attachments;
- O header MIME `Content-Transfer-Encoding` da parte SOAP deve ter o valor `8bit`.
Os headers MIME de cada parte da mensagem são preservados pelo Security Server sem modificação. O Security Server também suporta mensagens codificadas com **MTOM**.
Nesse caso, ele aceita mensagens MIME multipart em que o content-type da parte SOAP é:
```
application/xop+xml
```

---
#### Service Descriptions for SOAP
Serviços SOAP no X-Road são descritos usando:
```
WSDL 1.1
```
O X-Road suporta serviços versionados. Versões diferentes de um serviço representam pequenas mudanças técnicas na descrição do serviço.
Uma nova versão deve ser criada, por exemplo, quando houver:
- Reestruturação da descrição do serviço;
- Renomeação ou refatoração de tipos no XML Schema;
- Alteração de tipos ou nomes de campos.
Porém, quando a semântica do serviço ou o conteúdo dos dados muda, deve ser criado um novo serviço com um novo código.

---
#### Versionamento e Contratos
No contexto de contratos de fornecimento de serviço, os serviços são considerados sem versão. Isso significa que todas as versões do mesmo serviço são tratadas como equivalentes.
Essa regra também vale para restrições de controle de acesso nos Security Servers.
Ou seja, as permissões são aplicadas ao código do serviço, sem considerar a versão.
Para isso funcionar corretamente, todas as versões do mesmo serviço devem implementar o mesmo contrato.

---

#### Import ou Inline Schema
A estrutura de um documento XML é definida por um schema. Dentro de um arquivo WSDL, existem duas formas principais de incluir schemas:
- **Import**
- **Inline schema**

---
#### Import
No modelo **import**, o schema é definido em um arquivo separado e importado no WSDL usando o elemento `import`.

Esse recurso também pode ser usado para importar outros arquivos WSDL.

Ao usar `import`, é importante garantir que os arquivos importados estejam acessíveis para todas as aplicações cliente e serviço.

O Security Server tenta baixar os arquivos importados quando um novo WSDL é adicionado ou quando um WSDL existente é atualizado.

Porém, durante a troca de mensagens, o Security Server não precisa acessar esses arquivos externos.

A principal vantagem do import é tornar o WSDL mais modular.

---
#### Inline Schema
No modelo **inline schema**, o schema é incluído diretamente dentro do próprio arquivo WSDL. A desvantagem é que os tipos definidos no schema inline só podem ser usados naquele WSDL. Eles não podem ser compartilhados entre múltiplos arquivos WSDL.
Por outro lado, o inline schema reduz a dependência de recursos externos, já que tudo está dentro do mesmo arquivo.

---

#### Gerenciamento de Direitos de Acesso
Para serviços SOAP, o acesso pode ser definido no nível do **service code**. Quando um único WSDL contém múltiplos códigos de serviço SOAP, os direitos de acesso precisam ser definidos separadamente para cada código de serviço.

---

#### Autenticação Machine-to-Machine
A interface SOAP suporta autenticação TLS mútua entre o Security Server e o consumidor ou provedor SOAP. A autenticação baseada em JWT entre o Security Server e o sistema de informação não é suportada. Porém, é possível usar autenticação baseada em token entre o cliente do serviço e o provedor do serviço.
O X-Road Message Protocol for SOAP permite enviar SOAP headers arbitrários de ponta a ponta.
Também existe uma extensão que permite enviar tokens de segurança de forma padronizada usando o elemento:
```
securityToken
```
---

#### Resumo
O X-Road Message Protocol for SOAP define como serviços SOAP devem se comunicar com o Security Server.

Ele exige um formato específico baseado em SOAP 1.1, com headers obrigatórios, corpo no estilo document/literal-wrapped e suporte a anexos via MIME multipart.

Serviços SOAP normalmente exigem mais adaptação do que serviços REST.

Por isso, é comum usar um **Adapter Service** para converter mensagens para o formato esperado pelo X-Road.

As descrições dos serviços são feitas com WSDL 1.1, e o controle de acesso é definido no nível do código do serviço.

---

### Metada Services
O [[Security Server]] fornece um conjunto de métodos que podem ser usados pelos participantes do X-Road para descobrir quais serviços estão disponíveis e baixar descrições legíveis por máquina. Esses métodos são chamados de **metadata services**.
Eles são acessados por meio de dois protocolos:
- Service Metadata Protocol for SOAP;
- Service Metadata Protocol for REST.
Esses serviços ajudam clientes, portais e sistemas externos a descobrir membros, subsistemas, serviços disponíveis e suas respectivas descrições técnicas.
---
#### Metadata Services Disponíveis

| Serviço               | Descrição                                                                | Protocolo   |
| --------------------- | ------------------------------------------------------------------------ | ----------- |
| `listClients`         | Lista membros e subsistemas do X-Road                                    | SOAP / REST |
| `listCentralServices` | Lista todos os serviços centrais definidos em uma instância X-Road       | SOAP        |
| `listMethods`         | Lista todos os serviços fornecidos por um provedor específico            | SOAP / REST |
| `listAllowedMethods`  | Lista os serviços de um provedor que o cliente tem permissão para chamar | SOAP / REST |
| `getWsdl`             | Retorna a descrição WSDL de um serviço SOAP específico                   | SOAP        |
| `getOpenApi`          | Retorna a descrição OpenAPI de um serviço REST específico                | REST        |

---
#### Versões SOAP e REST

Os metadata services possuem versões separadas para serviços SOAP e REST.
A exceção é o serviço:
```
listCentralServices
```
Esse serviço possui apenas versão SOAP.

As versões SOAP de `listMethods` e `listAllowedMethods` retornam informações apenas sobre serviços SOAP.
As versões REST de `listMethods` e `listAllowedMethods` retornam informações apenas sobre serviços REST.

Portanto, para coletar informações sobre todos os serviços disponíveis, tanto SOAP quanto REST, é necessário chamar as duas versões do metadata service `listMethods`.

---

#### Finalidade dos Metadata Services
Os metadata services são usados para apoiar portais e softwares que precisam descobrir serviços automaticamente.
Com eles, é possível criar:
- Interfaces de usuário automaticamente;
- Stubs para aplicações clientes;
- Catálogos de serviços;
- Ferramentas de descoberta de serviços.

---
#### Fluxo de Descoberta de Serviços
O processo geral de descoberta pode ser feito em três etapas.
##### 1. Baixar a Lista de Clientes
Primeiro, o sistema baixa a lista de membros e subsistemas do X-Road usando:
```
listClients
```
Isso retorna uma lista de possíveis provedores de serviço que podem ser consultados posteriormente.
##### 2. Consultar Serviços do Provedor
Depois, o sistema se conecta ao provedor de serviço e obtém a lista de serviços oferecidos.
Isso pode ser feito com:
```
listMethods
listAllowedMethods
```
O `listMethods` retorna os serviços fornecidos por um provedor.
O `listAllowedMethods` retorna apenas os serviços que o cliente tem permissão para invocar.
Caso seja necessário coletar serviços SOAP e REST, as duas versões dos serviços devem ser chamadas.
##### 3. Baixar a Descrição do Serviço
Por fim, o sistema baixa a descrição técnica do serviço.
Para serviços SOAP, a descrição é obtida em formato:
```
WSDL
```
Para serviços REST, a descrição é obtida em formato:
```
OpenAPI
```
---
#### Resumo
Os metadata services permitem descobrir membros, subsistemas, serviços disponíveis e descrições técnicas dentro do X-Road.
Eles existem em versões separadas para SOAP e REST. Para obter uma visão completa dos serviços disponíveis em uma instância X-Road, é necessário consultar tanto os metadata services SOAP quanto os REST.
Esses serviços são importantes para automação, geração de catálogos, criação de clientes e descoberta dinâmica de serviços.

---
### Understanding error messages

O primeiro passo no processo de resolução de erros no [[x-road]] é entender onde a mensagem de erro foi gerada. Para isso, é importante conhecer os componentes envolvidos no fluxo da mensagem.

---
#### Componentes Envolvidos no Fluxo

Os principais componentes envolvidos são:
1. **Service Consumer**
2. **Consumer-side Security Server**, também chamado de **client proxy**
3. **Provider-side Security Server**, também chamado de **server proxy**
4. **Service Provider**
As mensagens de erro podem ser geradas pelos Security Servers ou pelo sistema de informação do provedor de serviço.
![[Pasted image 20260623005655.png]]

---
#### Origem das Mensagens de Erro
As mensagens de erro podem ser geradas por:
- Consumer-side Security Server;
- Provider-side Security Server;
- Service Provider Information System.
Erros gerados pelos Security Servers possuem uma estrutura fixa, específica para SOAP ou REST. Mesmo usando interfaces diferentes, as mensagens de erro SOAP e REST carregam as mesmas informações principais.

---
#### Campos de Erro
As mensagens de erro geradas pelo Security Server possuem três campos principais.

| REST      | SOAP          | Descrição                                                             |
| --------- | ------------- | --------------------------------------------------------------------- |
| `type`    | `faultCode`   | Código do erro. Indica onde o erro se originou e qual o tipo do erro. |
| `message` | `faultString` | Descrição mais detalhada do erro.                                     |
| `detail`  | `faultDetail` | ID único usado para buscar mais informações no log do proxy.          |

---
#### Campo type / faultCode
O campo `type`, no REST, ou `faultCode`, no SOAP, indica a origem e o tipo do erro.
Erros que começam com:
```
Server.ClientProxy
```
Têm origem no **consumer-side Security Server**, ou seja, no client proxy.
Nesse caso, informações mais detalhadas podem ser encontradas no log do Security Server consumidor:
```
/var/log/xroad/proxy.log
```
Erros que começam com:
```
Server.ServerProxy
```
Têm origem no **provider-side Security Server**, ou seja, no server proxy. Nesse caso, informações mais detalhadas podem ser encontradas no log do Security Server provedor:
```
/var/log/xroad/proxy.log
```
---
#### Campo message / faultString
O campo `message`, no REST, ou `faultString`, no SOAP, contém uma descrição mais detalhada do erro. Ele ajuda a entender o motivo da falha de forma mais direta.

---
#### Campo detail / faultDetail
O campo `detail`, no REST, ou `faultDetail`, no SOAP, contém um ID único do erro. Esse ID pode ser usado para procurar informações adicionais no log do proxy:
```
/var/log/xroad/proxy.log
```
---
#### Exemplo de Erro REST
Exemplo de erro gerado pelo client proxy no Security Server do consumidor:
```
{
  "type": "Server.ClientProxy.SslAuthenticationFailed",
  "message": "Client (SUBSYSTEM:PLAYGROUND/COM/1234567-8/TestClient) specifies HTTPS but did not supply TLS certificate",
  "detail": "2ea02d93-e0b8-4e2b-8c6e-fac20f53a3e3"
}
```
Nesse exemplo, o erro indica uma falha de autenticação SSL/TLS. O cliente especificou HTTPS, mas não forneceu o certificado TLS necessário. Como o erro começa com `Server.ClientProxy`, sua origem está no Security Server do lado consumidor.

---
#### Erros Gerados pelo Service Provider
Também podem ocorrer erros dentro do sistema de informação do provedor de serviço. Nesse caso, o provedor tem liberdade para retornar uma mensagem de erro customizada, desde que esteja alinhada com o X-Road Message Protocol.
Se a mensagem de erro retornada não seguir a estrutura com `type`, `message` e `detail`, provavelmente ela foi gerada pelo próprio sistema de informação do provedor.

---
#### Header X-Road-Error no REST
Quando o X-Road Message Protocol for REST é usado, o header HTTP abaixo é incluído na resposta se o erro tiver sido gerado por um dos Security Servers:
```
X-Road-Error
```
Esse header indica que o erro veio do Security Server do consumidor ou do Security Server do provedor.

---

#### Resumo
Na resolução de erros do X-Road, o ponto principal é identificar onde o erro foi gerado.
Se o erro começar com `Server.ClientProxy`, ele veio do Security Server do consumidor.
Se começar com `Server.ServerProxy`, ele veio do Security Server do provedor.
Se a mensagem não seguir a estrutura padrão de erro do X-Road, provavelmente ela foi gerada pelo sistema de informação do provedor.
O campo `detail` é importante porque permite buscar informações mais completas no arquivo de log:
```
/var/log/xroad/proxy.log
```
---
### Standalone Security Server

O **Standalone Security Server** é uma versão especial do [[X Road - Security Server]] pronta para uso em poucos minutos. Ele não exige o processo normal de instalação, configuração e registro de um Security Server tradicional.
Essa versão é voltada principalmente para testes durante o desenvolvimento de serviços X-Road.

---
#### Objetivo
O Standalone Security Server é destinado a:
- Desenvolvedores;
- Organizações que estão criando serviços para publicar via X-Road;
- Ambientes de teste;
- Experimentação rápida com X-Road.
Ele permite testar serviços sem precisar configurar toda a infraestrutura completa do X-Road.
---
#### Limitação Principal
O Standalone Security Server não consegue se comunicar com outros Security Servers.
Por isso, ele não deve ser usado como um Security Server real em uma instância X-Road de produção.
##### **Sua finalidade é apenas teste e desenvolvimento.**

---
#### Funcionamento
É possível adicionar novos serviços no Standalone Security Server e invocar esses serviços usando o próprio Security Server.
Ele já vem com dois subsistemas pré-configurados:
- Um subsistema para fornecer serviços;
- Um subsistema para consumir serviços.
Com isso, é possível simular o fluxo básico de consumo e fornecimento de serviços dentro do próprio ambiente standalone.

---
#### Independência de Serviços Externos
O Standalone Security Server não exige conexão com:
- Central Server;
- OCSP Service;
- Time-Stamping Service.
Isso torna sua configuração muito mais simples e rápida.
Depois de baixado, ele nem mesmo precisa de conexão com a internet para funcionar.

---
#### Uso com Docker
O Standalone Security Server está disponível no:

```
Docker Hub
```
https://hub.docker.com/r/niis/xroad-security-server-standalone
Isso facilita a execução em ambiente local, principalmente para testes rápidos e desenvolvimento de serviços.

---

#### Resumo
O Standalone Security Server é uma versão simplificada e pronta para uso do Security Server.
Ele é ideal para testes, desenvolvimento de serviços e experimentação com X-Road.
Sua principal vantagem é permitir testar consumo e fornecimento de serviços sem depender de Central Server, OCSP, TSA ou registro completo na infraestrutura X-Road.

Sua principal limitação é que ele não se comunica com outros Security Servers, portanto não é indicado para uso em produção.


# X-Road Service Developer - Cheat Sheet
%%Essa sessão está escondida porque contém respostas do questionário final do Módulo, nem todas estão aqui, mas grande parte do que me deparei está. Use por **conta e risco**.%%
> [!note]- X-Road Service Developer Cheat Sheet
> 
> ## Tracking de perguntas e respostas
> 
> > **What is the maximum number of Security Servers one subsystem can be associated with?**
> 
> - Unlimited
>     
> 
> > **Which component (1-4) generates the error message when the Security Server proxy returns an error which type is "Server.ServerProxy.SslAuthenticationFailed"?**
> 
> - 4
>     
> 
> > **Which of the following X-Road message headers is mandatory in all request messages?**
> 
> - client / X-Road-Client
>     
> 
> > **Which of the following entities can be used as a client in service requests?**
> 
> - Subsystem
>     
> - Member
>     
> 
> > **What is the maximum number of different subsystems that can be associated with one Security Server?**
> 
> - Unlimited
>     
> 
> > **What can the Standalone Security Server be used for?**
> 
> - Testing purposes in X-Road service development
>     
> 
> > **What monitoring feature provides details of the Security Servers such as operating system, memory, disk space, CPU load?**
> 
> - Environmental monitoring
>     
> 
> > **Which X-Road message header is used to identify the service client that initiates the service call?**
> 
> - client / X-Road-Client
>     
> 
> > **Which of the following content-types does the X-Road Message Protocol for REST support?**
> 
> - application/json
>     
> - text/xml
>     
> - text/html
>     
> - image/png
>     
> 
> > **Which of the following metadata services can be used to fetch a service description of a REST service?**
> 
> - getOpenApi
>     
> 
> > **What HTTP header is included in a REST error response when the error is generated by the Security Server?**
> 
> - X-Road-Error
>     
> 
> > **Which HTTP headers of the SOAP request message generated by the service client are preserved in the Security Server and forwarded to the service provider?**
> 
> - Content-Type
>     
> - SOAPAction
>     
> 
> > **What Linux distros are officially supported by the Security Server?**
> 
> - Ubuntu
>     
> - Red Hat
>     
> 
> > **What are the layers of the message transport protocol?**
> 
> - Transport layer
>     
> - Application layer
>     
> 
> > **Which of the following is not a metadata service provided by the Security Server?**
> 
> - getAllXRoadServices
>     
> 
> > **Which of the following metadata services can be used to fetch a service description of a SOAP service?**
> 
> - getWsdl
>     
> 
> > **With the message protocol for SOAP, are user-defined HTTP headers passed to the recipient unmodified by the Security Server?**
> 
> - No
>     
> 
> > **With the message protocol for REST, are user-defined HTTP headers passed to the recipient unmodified by the Security Server?**
> 
> - Yes
>     
> 
> > **Can the Security Server be run in a container?**
> 
> - Yes
>     
> 
> > **Which OpenAPI definition formats does the Security Server support?**
> 
> - YAML
>     
> - JSON
>     
> - YML
>     
> 
> > **In which part of the REST message should the user-defined headers be included?**
> 
> - HTTP headers
>     
> 
> > **What is special about the Standalone Security Server?**
> 
> - It is ready-to-use in minutes without the normal Security Server installation, configuration and registration process
>     
> - It cannot communicate with other Security Servers
>     
> - It does not require a connection to the Central Server, OCSP service or time-stamping service
>     
> 
> > **When a service client sends a REST request to the consumer Security Server, what is specified in the request URL?**
> 
> - The service that is invoked
>     
> 
> > **What component can be used as an X-Road compatible REST-SOAP converter?**
> 
> - REST Adapter Service
>     
> 
> > **What SOAP service description language does the Security Server support?**
> 
> - WSDL
>     
> 
> > **Which HTTP headers of the SOAP response message generated by the service provider are preserved in the Security Server and forwarded to the service client?**
> 
> - Content-Type
>     
> 
> > **What is recommended to use to protect the Security Server from unwanted access?**
> 
> - Firewall
>     
> 
> > **Does the Security Server provide automatic SOAP-REST conversion?**
> 
> - No
>     
> 
> > **In which part of the SOAP message must the user-defined headers be included?**
> 
> - SOAP Header
>
# Referências
## Introduction
#### Getting Started
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23560177-getting-started
#### Overview
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23560178-technology-overview
#### Model
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23560179-organisational-model
#### Architectures
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23560180-architecture

## Architecture
#### Intro
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23560222-introduction

#### Networking
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/58817051-networking

#### Communication with information systems
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23756504-communication-with-information-systems

#### Interfaces
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23560232-interfaces

#### Monitoring
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23648218-monitoring


## Data Exchange
#### Intro
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23560244-introduction

#### Transport Protocol 
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23560246-transport-protocol

#### Identifying entities
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23560260-identifying-entities

## Developing Services
#### Message Headers
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23561396-message-headers

#### Message protocol for REST
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23560247-message-protocol-for-rest

#### Message protocol for SOAP
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23560248-message-protocol-for-soap

#### Metadata Services
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23614983-metadata-services

#### Understanding Error Messages
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23560261-understanding-error-messages

#### Standalone Security Server
https://x-road.thinkific.com/courses/take/x-road-service-developer/texts/23611871-standalone-security-server