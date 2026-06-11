
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
| Ping iniciado pelo Cliente 1         | Funciona, porque tem regra permitindo ICMP entre os dois hosts.                                                              | Funciona, porque o Cliente 1 pode iniciar ICMP para o Cliente 2 com estado `NEW`, e o retorno é aceito como `ESTABLISHED`.                                        |
| Retorno da comunicação               | Precisa de regra explícita de volta, porque o firewall não entende que o pacote é resposta de uma conexão permitida.         | Não precisa de regra específica de volta, porque a regra `ESTABLISHED,RELATED` permite o retorno de conexões já autorizadas                           |
| HTTP Cliente 1 → Cliente 2           | Funciona, mas precisa de duas regras: uma para a ida na porta de destino 80 e outra para o retorno usando porta de origem 80. | Funciona, porque uma regra permite a nova conexão HTTP do Cliente 1 para o Cliente 2, e ela volta pela regra stateful.                          |
| Nova conexão iniciada pelo Cliente 2 | Falha, porque não tem regra permitindo que o Cliente 2 inicie uma nova conexão TCP para o Cliente 1.                             | Falha, porque o Cliente 2 não tem permissão para iniciar conexões novas, visto que a regra `ESTABLISHED,RELATED` só aceita respostas de conexões já iniciadas. |
| Quantidade de regras                 | Maior, porque tem que criar regras separadas para ida e volta do tráfego permitido.                                           | Menor, porque uma regra geral de estado trata o retorno das conexões permitidas.                                                                                |
| Facilidade de administração          | Menor, porque as regras ficam mais repetitivas e exigem mais cuidado com os dois sentidos da comunicação.                       | Maior, porque a lógica fica mais simples: libera-se o início da conexão permitida e o retorno é tratado automaticamente.                                          |

## Texto sobre a diferença entre firewall de pacotes e firewall stateful

No firewall de pacotes cada pacote é analisado de forma isolada, usando informações como IP de origem, IP de destino, protocolo e porta. No qual, é necessário criar regras separadas para a ida e para a volta da comunicação.

Já no firewall stateful o firewall acompanha o estado das conexões. Assim, quando uma conexão é permitidaos pacotes de retorno podem ser aceitos automaticamente como parte de uma conexão já estabelecida, reduzindo a quantidade de regras e deixando a política do firewall mais simples de administrar.

## Questões

**1. O que diferencia um firewall stateful de um firewall de pacotes simples?**

Um firewall de pacotes simples analisa cada pacote de forma isolada, usando informações como IP de origem, IP de destino, protocolo e porta. Já um firewall stateful acompanha o estado das conexões. Assim, ele consegue identificar se um pacote está iniciando uma conexão nova ou se faz parte de uma conexão que já foi permitida.

**2. Qual a função de `-m conntrack --ctstate ESTABLISHED,RELATED`?**

Essa regra usa o módulo `conntrack` para verificar o estado da conexão. O estado `ESTABLISHED` permite pacotes que fazem parte de uma conexão já estabelecida. O estado `RELATED` permite pacotes relacionados a uma conexão já existente. Com isso, o firewall aceita automaticamente o tráfego de retorno de conexões autorizadas.

**3. Por que o retorno da conexão HTTP não precisou de regra explícita no sentido inverso?**

Porque a conexão HTTP foi iniciada pelo Cliente 1 e permitida pelo firewall. Depois disso, os pacotes de resposta do Cliente 2 foram reconhecidos como parte de uma conexão já estabelecida. Por isso, eles foram aceitos pela regra `ESTABLISHED,RELATED`, sem precisar criar uma regra específica de volta.

**4. O que caracteriza um pacote no estado `NEW`?**

Um pacote no estado `NEW` é um pacote que está iniciando uma nova conexão. No laboratório, isso acontece quando o Cliente 1 tenta iniciar um ping ou uma conexão HTTP para o Cliente 2.

**5. Qual a principal vantagem de usar regras stateful em relação ao Laboratório 10?**

A principal vantagem é reduzir a quantidade de regras necessárias. No Lab 10, era preciso criar regras de ida e de volta. No Lab 10B, basta permitir a nova conexão e deixar o retorno ser tratado automaticamente pela regra `ESTABLISHED,RELATED`.

**6. Por que o Cliente 2 não conseguiu iniciar novas conexões para o Cliente 1?**

Porque as regras do firewall só permitem novas conexões iniciadas pelo Cliente 1 para o Cliente 2. O Cliente 2 só consegue enviar pacotes de resposta quando eles pertencem a uma conexão já estabelecida. Como uma nova conexão iniciada pelo Cliente 2 não se encaixa nessa situação, ela é bloqueada pela política padrão `DROP`.

**7. O que mudou na quantidade e na lógica das regras entre Laboratório 10 e Laboratório 10B?**

No Laboratório 10, a lógica era baseada em regras separadas para cada sentido da comunicação. Era necessário liberar a ida e também o retorno. No Laboratório 10B, a lógica ficou mais simples, pois o firewall acompanha o estado da conexão. Assim, o retorno das conexões permitidas passa automaticamente, reduzindo a repetição de regras.

**8. Em que tipo de ambiente um firewall stateful tende a ser mais adequado?**

Um firewall stateful é mais adequado em redes onde há muitas conexões entrando e saindo, como redes corporativas, servidores, laboratórios com várias máquinas e ambientes que precisam de controle mais organizado. Ele facilita a administração porque evita a criação manual de muitas regras de retorno.

**9. O firewall stateful elimina a necessidade de política de bloqueio padrão? Explique.**

Não. Mesmo usando firewall stateful, ainda é importante manter uma política padrão de bloqueio, como `DROP`. Isso garante que tudo que não foi permitido explicitamente seja bloqueado. O stateful ajuda a liberar automaticamente respostas de conexões autorizadas, mas não significa que todo tráfego deve ser aceito.

**10. O que a atividade comparativa mostrou de forma mais clara sobre a diferença entre os dois modelos?**

A atividade mostrou que o firewall de pacotes exige regras mais detalhadas e repetidas, principalmente para ida e volta da comunicação. Já o firewall stateful entende o estado das conexões e permite automaticamente o retorno de tráfegos autorizados. Com isso, o Lab 10B ficou mais simples, mais organizado e mais fácil de administrar.
