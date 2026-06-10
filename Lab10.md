# Laboratorio 10 - Enzo Porto e Mello - 221006727

## Objetivo
Implementar um firewall de pacotes em uma máquina Linux no PNetLab, posicionada entre duas máquinas Linux básicas - preferencialmente Linux Tinycore-6.4 - aplicando regras com `iptables` para controlar o tráfego entre duas redes distintas com base em endereço IP, protocolo e porta.

## Prints
- Topologia
<img width="695" height="465" alt="image" src="https://github.com/user-attachments/assets/d92bc858-e7d4-43ff-82ae-0c81a0a8f3fe" />

-Configuração dos cliente 1

<img width="524" height="52" alt="image" src="https://github.com/user-attachments/assets/7c713d24-d13c-4b5e-b01a-14e2d2c6b58a" />

<img width="816" height="188" alt="image" src="https://github.com/user-attachments/assets/8afea057-d909-4da0-b257-65d20bf616b7" />

-Configuração dos cliente 2

<img width="552" height="67" alt="image" src="https://github.com/user-attachments/assets/cb6dccdb-a9c3-44a5-951b-19a3509d6a31" />

<img width="554" height="38" alt="image" src="https://github.com/user-attachments/assets/e2d75846-6f78-4e1b-88ad-15cf8e2b8114" />

-Configuração do servidor

<img width="802" height="215" alt="image" src="https://github.com/user-attachments/assets/b0510390-4077-4b2d-8741-3e750963a2fa" />

<img width="739" height="132" alt="image" src="https://github.com/user-attachments/assets/13d14649-51ae-4c49-a2cb-036373abe368" />

<img width="669" height="145" alt="image" src="https://github.com/user-attachments/assets/c7323ad6-ffaa-422e-b7fb-736065649a3f" />


## Testes

<img width="901" height="426" alt="image" src="https://github.com/user-attachments/assets/45febc9f-4f48-4ecf-9a65-67010210a743" />

<img width="377" height="117" alt="image" src="https://github.com/user-attachments/assets/64292882-310f-42a6-8a76-9fa15e9478f8" />

<img width="401" height="62" alt="image" src="https://github.com/user-attachments/assets/d7558a0b-7efc-4caa-830d-7fc1ef3fd908" />

## Questões
### O que caracteriza um firewall de pacotes?
     
O firewall de pacotes filtra o trafegos olhando o cabeçalho dos pacotes e colocando regras de permissão e bloqueio

###  Quais campos do pacote foram usados nas regras deste laboratório?
     
Neste laboratório, as regras usadas foram:
  - Endereço IP de origem;
  - Endereço IP de destino;
  - Protocolo;
  - Porta de destino;
  - Porta de origem;
     
###  Por que foi necessário ativar o IP forwarding no Linux?
     
Como o Cliente 1 e o Cliente 2 estavam em redes diferentes, o linux Firewall precisava atuar como roteador, recebendo pacotes por uma interface e enviando pela outra.
     
###  Qual é a função da cadeia FORWARD no iptables?
     
A cadeia FORWARD no iptables tem a função de filtrar pacotes que atravessam a máquina Linux, ou seja, pacotes que chegam por uma interface e precisam ser encaminhados para outra rede.
    
###  Por que o tráfego não permitido foi bloqueado mesmo sem regras específicas para todos os protocolos?

O tráfego não permitido foi bloqueado porque a política padrão da cadeia FORWARD foi configurada como DROP. Assim, somente os pacotes que correspondiam às regras de permissão eram aceitos. Qualquer outro tráfego, mesmo sem uma regra específica de bloqueio, era descartado automaticamente pelo iptables
     
###  Qual a diferença entre permitir HTTP e permitir ICMP?
     
Permitir HTTP é liberar um serviço de aplicação que usa o protocolo TCP. Já permitir ICMP é liberar mensagens de controle da rede, como o ping, que não usam portas TCP ou UDP     
     
###  O que muda quando a política padrão da cadeia FORWARD é DROP?
     
Quando a política padrão da cadeia FORWARD é definida como DROP, todo tráfego que atravessa o firewall passa a ser bloqueado por padrão. E so pacotes que possuem regras ACCEPT podem passar.     
     
###  Por que esse laboratório ainda não é considerado um firewall stateful?
     
Esse laboratório ainda não é considerado um firewall stateful porque as regras configuradas não acompanham o estado das conexões, o firewall analisa cada pacote individualmente, e por isso o tráfego de retorno também precisou ser permitido por regras explícitas. Em um firewall stateful, o retorno de uma conexão já estabelecida poderia ser reconhecido e liberado automaticamente.     
     
###  Qual a importância da ordem das regras no `iptables`?
      
Quando um pacote chega, o `iptables` compara esse pacote com a primeira regra, depois com a segunda, depois com a terceira, e assim por diante, e quando ele encontra uma regra que combina com o pacote, ele aplica a ação daquela regra, como ACCEPT ou DROP.      
      
###  Quais vantagens práticas surgem ao usar hosts Linux básicos no lugar de VPCs neste laboratório?
      
As vantegens de usar linux de VPCs é porque é possível configurar endereços IP e rotas manualmente, executar ferramentas de teste como ping, curl, wget, telnet e nc, além de subir um servidor HTTP simples para testar a liberação da porta 80, enquanto as VPCs seriam mais limitadas para esse tipo de prática.      
      
###  Qual a diferença entre bloquear o ping com `sysctl` e bloquear ICMP com `iptables`?
      
Bloquear o ping com `sysctl` altera o comportamento da própria máquina Linux, fazendo o firewall ignorar requisições ICMP Echo destinadas a ele mesmo. Já bloquear ICMP com iptables atua como regra de firewall, podendo impedir que pacotes ICMP atravessem a máquina entre uma rede e outra      
      
