

| Origem          | Destino                               | Portas            | Protocolo | Observação                                      |
| --------------- | ------------------------------------- | ----------------- | --------- | ----------------------------------------------- |
| Security Server | Central Server                        | 80, 443, 4001     | TCP       | Comunicação com o Central Server                |
| Security Server | Management Security Server            | 5500, 5577        | TCP       | Comunicação de gerenciamento                    |
| Security Server | OCSP Service                          | 80 / 443          | TCP       | Verificação de validade dos certificados        |
| Security Server | Timestamping Service                  | 80 / 443          | TCP       | Serviço de carimbo de tempo                     |
| Security Server | Data Exchange Partner Security Server | 5500, 5577        | TCP       | Comunicação com Security Server parceiro        |
| Security Server | Producer Information System           | 80, 443 ou outras | TCP       | Destino na rede interna                         |
| Security Server | ACME Server                           | 80 / 443          | TCP       | Emissão ou renovação automática de certificados |

---