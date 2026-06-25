[[X Road - Time-Stamping Authority (TSA)]]

Todas as mensagens enviadas via X-Road são registradas com data e hora pelo Servidor de Segurança. O objetivo do registro de data e hora é certificar a existência de itens de dados em um determinado momento. O TSA fornece um serviço de registro de data e hora que o Servidor de Segurança utiliza para registrar todas as solicitações e respostas de entrada e saída. Somente TSAs 
confiáveis, definidos no Servidor Central, podem ser utilizados.

A autoridade de registro de data e hora deve implementar o protocolo de registro de data e hora suportado pelo X-Road. O X-Road utiliza registro de data e hora em lote, o que reduz a carga do serviço de registro de data e hora. A carga não depende do número de mensagens trocadas pelo X-Road, mas sim do número de servidores de segurança no sistema.