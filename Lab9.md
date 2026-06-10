# Laboratorio 9 - Enzo Porto e Mello - 221006727

## Objetivo
Implementar um backbone MPLS simplificado na rede do provedor, dando continuidade ao Lab 08, de modo a compreender o papel dos roteadores CE, PE e P, habilitar o transporte por rótulos no núcleo da operadora e verificar o funcionamento do backbone MPLS sobre uma infraestrutura previamente estabelecida com OSPF e BGP.

## Prints
- Topologia
<img width="794" height="295" alt="image" src="https://github.com/user-attachments/assets/4a3fcd2e-3501-42b9-88ec-e0afeb309b53" />

- show ip ospf neighbor do R2
<img width="639" height="125" alt="image" src="https://github.com/user-attachments/assets/8c7a741d-acee-4bcb-8008-ce6d86ffc6c4" />

- show mpls interfaces
<img width="657" height="102" alt="image" src="https://github.com/user-attachments/assets/3949f9b6-8491-4275-b759-1298cc49f406" />
<img width="658" height="100" alt="image" src="https://github.com/user-attachments/assets/5c737b4e-66b8-4cb1-9aea-ddc83ec84ae2" />
<img width="653" height="133" alt="image" src="https://github.com/user-attachments/assets/2d075c82-a49d-4955-b75c-e0fc01d9112e" />

- show mpls ldp neighbor
<img width="497" height="131" alt="image" src="https://github.com/user-attachments/assets/79eb0fab-c075-42a6-9a1b-36ed9e54dcd7" />
<img width="514" height="136" alt="image" src="https://github.com/user-attachments/assets/aaeeb120-e2ff-43a1-89b7-d01fdf4b06d0" />
<img width="505" height="242" alt="image" src="https://github.com/user-attachments/assets/1c24d7b4-a891-4aa6-bebb-18151d5e10f2" />

  
- show mpls forwarding-table
<img width="657" height="378" alt="image" src="https://github.com/user-attachments/assets/4b4f8d5b-cbbd-4fc0-81e3-cffdd055b76d" />
<img width="657" height="211" alt="image" src="https://github.com/user-attachments/assets/08d5f503-0e43-4889-9895-0d2af50de723" />
<img width="660" height="175" alt="image" src="https://github.com/user-attachments/assets/e91f615d-8187-4797-862d-b73869344573" />

## Questões
### Qual é a principal diferença entre roteamento IP tradicional e encaminhamento com MPLS?
  
No roteamento IP tradicional, cada roteador analisa o endereço IP de destino do pacote e consulta sua tabela de roteamento para decidir o próximo salto. Já no MPLS, os pacotes recebem rótulos, chamados labels, e o encaminhamento dentro do backbone passa a ser feito com base nesses rótulos.

isso faz com que o MPLS evite que cada roteador precise analisar todo o cabeçalho IP a cada salto. E cada roteador apenas verifica o label, troca por outro quando necessário e encaminha o pacote pelo caminho definido.

### Qual é a função do OSPF dentro do backbone do provedor?
  
O OSPF funciona como o protocolo de roteamento interno do backbone da operadora. Ele é responsável por permitir que os roteadores do provedor conheçam os caminhos internos da rede. Onde nesse laboratorio faz com que os ISPs conheçam o caminho interno da sua rede, e assim distribuir labels e pacotes corretamente dentro do backbone.

### Qual é o papel dos roteadores PE?
  
Os roteadores PE (ISP1 e ISP2), ou Provider Edge, são os roteadores de borda da operadora que ficam na fronteira entre a rede do cliente e a rede do provedor. Onde recebem o tráfego vindo do cliente e fazem a entrada desse tráfego na nuvem da operadora e são responsáveis por manter a comunicação entre a borda da rede e o backbone MPLS.

### Qual é o papel do roteador P?
  
O roteador P (ISP3), ou Provider, é o roteador do núcleo da rede da operadora, no qual não se conecta diretamente ao cliente. Sua função principal é transportar o tráfego dentro do backbone do provedor usando MPLS. Ele encaminha os pacotes com base nos labels, sem precisar lidar diretamente com as redes do cliente.


### Por que o cliente normalmente não precisa configurar MPLS no seu roteador?
  
O cliente normalmente não precisa configurar MPLS porque é uma tecnologia usada dentro da rede do provedor. Nesse lab o R1 é o roteador CE, o roteador do cliente, participa da comunicação com o provedor, mas não precisa conhecer a lógica interna do backbone MPLS, pois quem cuida dos labels e o encaminhamento MPLS são os roteadores da operadora.

### Como o Lab 09 complementa o Lab 08?
  
O Lab 09 é a continuação do Lab 08.
O Lab 08 focava na comunicação entre a empresa e os provedores, usando BGP e políticas de caminho. E com isso, o Lab 09 foca no interior da rede da operadora, no qual adiciona OSPF no backbone e habilita MPLS nos enlaces internos, permitindo observar como o tráfego pode ser transportado por rótulos dentro da nuvem do provedor.

### O que significa dizer que o MPLS atua como tecnologia de “camada 2,5”?
  
Dizer que o MPLS atua como uma tecnologia de “camada 2,5” significa que ele fica entre a camada 2 e a camada 3, visto que não funciona apenas como Ethernet ou enlace físico e o encaminhamento principal dentro do backbone não é feito apenas pelo endereço IP.

### Por que o backbone precisa de um IGP estável antes da ativação do MPLS?
  
O backbone precisa de um IGP estável, como o OSPF, porque o MPLS depende das rotas internas para saber quais caminhos existem dentro da rede. Primeiro, os roteadores precisam conseguir alcançar uns aos outros pelo roteamento IP interno. Depois que essa conectividade está funcionando, o LDP pode distribuir os labels corretamente.

## Conclusão

O Lab 09 mostra que o MPLS é usado dentro da rede da operadora para encaminhar tráfego com base em rótulos. O cliente continua usando comunicação IP comum, enquanto o provedor organiza internamente seu backbone com OSPF, LDP e MPLS.


