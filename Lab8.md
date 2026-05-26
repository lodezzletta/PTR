# Laboratorio 8 - Enzo Porto e Mello - 221006727

## Objetivo
Aplicar políticas de roteamento BGP e integrar o BGP ao OSPF em um cenário já previamente configurado, realizando apenas os ajustes específicos deste laboratório para anúncio de prefixos, escolha de caminho preferencial de saída, propagação controlada de rota default e análise de redundância entre provedores.


## Teste de Falha
### todos os enlaces funcionando
- `show ip bgp summary`
<img width="639" height="207" alt="image" src="https://github.com/user-attachments/assets/c32e7645-afd9-4ad5-817f-730c4a4cde2c" />


- `show ip bgp`
<img width="633" height="301" alt="image" src="https://github.com/user-attachments/assets/f8b75280-9812-44dd-bef0-b5d2dc79fe7d" />


- `show ip route`
<img width="655" height="523" alt="image" src="https://github.com/user-attachments/assets/93f67bb4-c9c4-4988-9a9a-07c0dae6fc29" />

Com todos os enlaces ativos, o BGP possui dois caminhos para as redes externas: um via ISP1, pelo next hop 10.10.10.10, e outro via ISP2, pelo next hop 10.2.0.2. Como o caminho via ISP1 recebeu Weight 200, ele foi escolhido como melhor caminho.
### tirando um enlace
- `show ip bgp summary`
<img width="624" height="211" alt="image" src="https://github.com/user-attachments/assets/31689ba6-b7bb-4451-b1f7-64044191282b" />



- `show ip bgp`
<img width="566" height="297" alt="image" src="https://github.com/user-attachments/assets/e0d57f6f-7e07-4c9b-b53c-088f2704c9a4" />

Ao desligar apenas um enlace com o ISP1, a sessão BGP com o ISP1 continuou funcionando, pois ainda existia outro caminho físico até a loopback do ISP1.

### Tirando todos os enlaces
- `show ip bgp summary`
<img width="634" height="203" alt="image" src="https://github.com/user-attachments/assets/cef9e901-ed66-49bc-9906-da9403363298" />



- `show ip bgp`
<img width="584" height="303" alt="image" src="https://github.com/user-attachments/assets/6c2f4e04-f098-4867-ae32-f459410d1e21" />



- `show ip route`
<img width="639" height="434" alt="image" src="https://github.com/user-attachments/assets/081a9061-28bf-4537-88bb-3ff13b6b1016" />

Ao desligar todos os enlaces com o ISP1, o caminho via 10.10.10.10 deixou de ser utilizado e o roteador passou a escolher o caminho via 10.2.0.2, correspondente ao ISP2.

## Perguntas
### Qual é o papel do OSPF neste laboratório?
O OSPF representa o roteamento interno da empresa, e ele é utilizado para divulgar as redes internas, como a o _192.168.0.0/24_ e o _loopback_ do roteador da empresa. A ideia é que os roteadores internos saibam sair da rede sem precisar conhecer todas as rotas externas da Internet.

### Qual é o papel do BGP neste laboratório?
O BGP é usado na borda da rede da empresa, para trocar rotas com os provedores. Ele anuncia o bloco público da empresa _200.18.245.64/27_, receber rotas externas e aplicar política de saída, sendpo o ISP1 como caminho principal e o ISP2 como backup.

### Por que o bloco _200.18.245.64/27_ é anunciado externamente, mas a rede _192.168.0.0/24_ não?
Porque o bloco 200.18.245.64/27 é o bloco público da empresa, então ele precisa ser anunciado para que outras redes na Internet saibam como chegar até ele. E a rede 192.168.0.0/24 é uma rede interna privada.

### Qual a vantagem de formar a sessão BGP com o ISP1 por loopback?
A vantagem é aumentar a redundância, visto que como existem dois links físicos entre a empresa e o ISP1, usar a loopback permite que a sessão BGP continue funcionando mesmo se um dos enlaces cair. 

### Qual é a função do comando `update-source Loopback1`?
Esse comando faz o BGP usar o endereço da interface Loopback1 como origem da sessão BGP, e sem ele o roteador usaria o IP da interface física de saída. Assim, contanto que exista um caminho até o vizinho, não teria problema se um dos links caisse

### Qual é a função do comando `ebgp-multihop 2`?
O comando `ebgp-multihop 2` permite formar uma sessão eBGP mesmo quando o vizinho não está a apenas um salto direto. Porque a sessão com o ISP1 é feita entre loopbacks, e não diretamente entre os IPs das interfaces físicas, fazendo o BGP precisa aceitar mais de um salto.

### Como verificar, no `show ip bgp`, qual ISP está sendo preferido?
Para saber qual ISP está sendo preferido, você olha a rota desejada e vê qual próximo salto está com `>`, no qual esse simbolo mostra é o melhor caminho. 

### Por que é mais adequado propagar apenas a default route no OSPF?
Porque isso deixa o OSPF mais limpo e simples, no qual em vez de passar várias rotas externas dentro da rede interna, o roteador da empresa anuncia apenas uma rota padrão 0.0.0.0/0. Assim, os roteadores internos só precisam saber: “para sair para fora, envie para o roteador de borda”. 

### O que acontece quando o enlace principal com o ISP1 falha?
Se apenas um dos links físicos com o ISP1 falhar, a sessão BGP com o ISP1 ainda pode continuar ativa, porque ela foi formada usando loopback e existe outro caminho físico até o ISP1. Mas se toda a conectividade com o ISP1 cair, o tráfego deve passar a sair pelo ISP2, que funciona como um plano B.

### Qual a diferença entre usar OSPF para a rede interna e BGP para a borda?
O OSPF é usado dentro da empresa, para o roteamento interno, pois ele é mais adequado para divulgar redes internas e manter a conectividade dentro do mesmo domínio. Já o BGP é usado na borda, entre sistemas autônomos diferentes, como o ISP1 e o ISP2. E o BGP também deixa aplicar políticas, como preferir um provedor e deixar outro como backup.


## Prints dos resultados
- `Topologia`
<img width="735" height="334" alt="image" src="https://github.com/user-attachments/assets/db1150b9-f9d6-4a53-8cca-278d04982b68" />

- `show ip bgp summary`
<img width="639" height="207" alt="image" src="https://github.com/user-attachments/assets/c32e7645-afd9-4ad5-817f-730c4a4cde2c" />


- `show ip bgp`
<img width="633" height="301" alt="image" src="https://github.com/user-attachments/assets/f8b75280-9812-44dd-bef0-b5d2dc79fe7d" />


- `show ip route`
<img width="655" height="523" alt="image" src="https://github.com/user-attachments/assets/93f67bb4-c9c4-4988-9a9a-07c0dae6fc29" />

- `show ip ospf`
<img width="503" height="590" alt="image" src="https://github.com/user-attachments/assets/f2abff9b-34f7-4f76-a747-7d5404f8a3d5" />
