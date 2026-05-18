# Laboratório 6 - Enzo Porto e Mello - 221006727

## Objetivo
Configurar o protocolo BGP no roteador da empresa para que ela possa anunciar seu prefixo público à Internet por meio de seus provedores.

## Comandos usados
<img width="389" height="486" alt="image" src="https://github.com/user-attachments/assets/1ebb1e4e-06d5-432b-81e8-06597401c75d" />

## Topologia e resultados dos comandos

<img width="889" height="578" alt="image" src="https://github.com/user-attachments/assets/158e0b59-0f4d-4aaf-bcbe-8f34484fa5fd" />
<img width="568" height="449" alt="image" src="https://github.com/user-attachments/assets/d74b2cd6-7d74-475e-b1fd-7d79465d6430" />
<img width="641" height="436" alt="image" src="https://github.com/user-attachments/assets/d10f5c5c-7b6d-4d92-9ef1-c118c6f6900f" />
<img width="658" height="78" alt="image" src="https://github.com/user-attachments/assets/721752bb-e02d-4bdc-a5c3-25a970861053" />
<img width="620" height="202" alt="image" src="https://github.com/user-attachments/assets/cb9a80db-7365-4f39-a4c7-c6726b27af82" />
<img width="554" height="442" alt="image" src="https://github.com/user-attachments/assets/080c35ad-1b00-4c65-b2cf-ab717c00f334" />

## Perguntas

### 1. Qual é a função do BGP nesse cenário?

O BGP permite que a empresa, que está no AS 1000, anuncie seu prefixo público 200.18.245.64/27 para os provedores ISP1 e ISP2. Assim, outros sistemas autônomos conseguem aprender uma rota para chegar até a rede da empresa.

### 2. Por que a sessão com o ISP1 usa endereço de loopback?

Porque a loopback não depende de uma interface física específica. Como existem dois enlaces entre o R1 e o ISP1, a sessão BGP pode continuar funcionando mesmo se um dos enlaces cair, desde que ainda exista rota até a loopback.

### 3. Por que foi necessário configurar ebgp-multihop 2?

Porque a sessão BGP com o ISP1 não é feita usando o IP da interface física diretamente conectada. Ela é feita usando a loopback 10.10.10.10. Por isso, é necessário permitir que o eBGP funcione com mais de um salto.

### 4. Qual a função do update-source Loopback1?

O comando update-source Loopback1 faz o R1 usar o endereço da sua loopback, 11.11.11.11, como origem da sessão BGP com o ISP1.

### 5. Por que foi criada a rota ip route 200.18.245.64 255.255.255.224 Null0?

Porque o BGP só anuncia uma rede com o comando network se essa rede já existir na tabela de roteamento. A rota para Null0 cria essa entrada na tabela e permite que o prefixo 200.18.245.64/27 seja anunciado.

### 6. Qual a diferença entre o pareamento com o ISP1 e com o ISP2?

Com o ISP1, o pareamento BGP é feito usando loopbacks. Por isso, são necessários os comandos update-source, ebgp-multihop e rotas estáticas para alcançar a loopback. Com o ISP2, o pareamento é feito diretamente pelo IP da interface física 10.2.0.2, então a configuração é mais simples.

## Conclusão

Neste laboratório, foi configurado o BGP no roteador R1, pertencente ao AS 1000, para anunciar o prefixo público aos provedores ISP1 e ISP2. A sessão com o ISP1 foi feita usando loopbacks, usando os comandos update-source Loopback1, ebgp-multihop 2 e rotas estáticas até a loopback do ISP1. Já a sessão com o ISP2 foi feita diretamente pelo endereço da interface física.
