[[X Road - Information System]]

O Sistema de Informação produz e/ou consome serviços via X-Road e pertence a um membro do X-Road. O X-Road suporta o consumo e a produção de serviços REST e SOAP. No entanto, o X-Road não oferece conversões automáticas entre diferentes tipos de mensagens e serviços.

Para um Sistema de Informação consumidor de serviços, o Servidor de Segurança atua como um ponto de entrada para todos os serviços X-Road. O consumidor pode descobrir os membros X-Road registrados e seus serviços disponíveis utilizando o protocolo de metadados X-Road.

Um sistema de informação provedor de serviços implementa um serviço REST e/ou SOAP e o disponibiliza através do X-Road. Os serviços REST existentes não requerem alterações – podem ser publicados tal como estão. Já os serviços SOAP devem implementar o protocolo de mensagens SOAP do X-Road. As descrições dos serviços REST são definidas utilizando a especificação OpenAPI3, e as descrições dos serviços SOAP são definidas utilizando WSDL. Os consumidores de serviços podem baixar as descrições dos serviços utilizando o protocolo de metadados do X-Road.