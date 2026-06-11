
- topologia
<img width="560" height="384" alt="image" src="https://github.com/user-attachments/assets/e1e1d056-ae8c-42f0-bccf-091cc41a4bac" />

- configuração do linux central
<img width="924" height="98" alt="image" src="https://github.com/user-attachments/assets/af68df7d-04b4-4ee0-a1e3-13dd64f738e7" />
<img width="1266" height="139" alt="image" src="https://github.com/user-attachments/assets/2bba739e-46ca-458c-a3e4-595da3cb7247" />

- configuração do linux cliente 1
<img width="524" height="52" alt="image" src="https://github.com/user-attachments/assets/2009743d-2428-4bc1-a2d0-ffdec57eaba1" />
<img width="816" height="188" alt="image" src="https://github.com/user-attachments/assets/b8b5ebbf-b3c6-41e2-9e3d-edb4d0af4c0b" />

- configuração do linux cliente 2
<img width="552" height="67" alt="image" src="https://github.com/user-attachments/assets/186ef5f0-f8db-4e8a-bac6-092a0ebe003c" />
<img width="554" height="38" alt="image" src="https://github.com/user-attachments/assets/91e961d6-f152-465a-94ea-c2b95deaa10c" />


- wget
<img width="1147" height="312" alt="image" src="https://github.com/user-attachments/assets/a49635cc-1123-4a13-8a11-1a6be9d0b7c9" />

- Evidências dos testes
  - Ping do cliente 1 funcionando
      <img width="665" height="90" alt="image" src="https://github.com/user-attachments/assets/72778722-01d7-4420-a34c-ab430e1bb9ad" />
  - Ping do cliente 2 falhando
      <img width="632" height="58" alt="image" src="https://github.com/user-attachments/assets/f7edb4dc-b7eb-4c74-9275-d4ba46c02c92" />
  - HTTP incioando pelo cliente 1  funcionando
      <img width="1149" height="273" alt="image" src="https://github.com/user-attachments/assets/954f9ccb-d4c9-41a5-8a88-af2374f2b22d" />
  - Teste do telnet falhando
      <img width="581" height="70" alt="image" src="https://github.com/user-attachments/assets/8bf0222a-5af3-414f-bc9a-a0962a70885e" />
      <img width="781" height="45" alt="image" src="https://github.com/user-attachments/assets/5ed26ec4-4dec-4e37-8118-a50e3f3cc17e" />

## Tabela
## Tabela comparativa entre Laboratório 10 e Laboratório 10B

| Teste                                | Laboratório 10 - Filtro de pacotes                                                                                            | Laboratório 10B - Stateful                                                                                                                                      |
| ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Ping iniciado pelo Cliente 1         | Funciona, pois existe regra permitindo ICMP entre os dois hosts.                                                              | Funciona, pois o Cliente 1 pode iniciar ICMP para o Cliente 2 com estado `NEW`, e o retorno é aceito como `ESTABLISHED`.                                        |
| Retorno da comunicação               | Precisa de regra explícita de volta. O firewall não entende sozinho que o pacote é resposta de uma conexão permitida.         | Não precisa de regra específica de volta. A regra `ESTABLISHED,RELATED` permite automaticamente o retorno de conexões já autorizadas.                           |
| HTTP Cliente 1 → Cliente 2           | Funciona, mas precisa de duas regras: uma para a ida na porta de destino 80 e outra para o retorno usando porta de origem 80. | Funciona com menos regras: uma regra permite a nova conexão HTTP do Cliente 1 para o Cliente 2, e o retorno passa pela regra stateful.                          |
| Nova conexão iniciada pelo Cliente 2 | Falha, pois não há regra permitindo que o Cliente 2 inicie uma nova conexão TCP para o Cliente 1.                             | Falha, pois o Cliente 2 não tem permissão para iniciar conexões novas. A regra `ESTABLISHED,RELATED` só aceita respostas de conexões já iniciadas e permitidas. |
| Quantidade de regras                 | Maior, porque precisa criar regras separadas para ida e volta do tráfego permitido.                                           | Menor, porque uma regra geral de estado trata o retorno das conexões permitidas.                                                                                |
| Facilidade de administração          | Menor, pois as regras ficam mais repetitivas e exigem mais cuidado com os dois sentidos da comunicação.                       | Maior, pois a lógica fica mais simples: libera-se o início da conexão permitida e o retorno é tratado automaticamente.                                          |

