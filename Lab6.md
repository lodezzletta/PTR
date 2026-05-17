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
O BGP permite que a empresa anuncie seu prefixo público 200.18.245.64/27 para os provedores ISP1 e ISP2, com isso outros ASs consiguem aprender uma rota para chegar à rede da empresa.

### 2. Por que a sessão com o ISP1 usa endereço de loopback?
Porque, pelo loopback, a sessão BGP não depende de uma interface física específica, pois caso um enlace cair, a sessão pode continuar pelo outro, desde que ainda exista rota até a loopback.

### 3. Por que foi necessário configurar ebgp-multihop 2?
Porque a sessão BGP com o ISP1 não é feita com o IP da interface diretamente conectada, e sim com o loopback. Logo, o ```ebgp-multihop 2``` permite fechar BGP com um vizinho que não está diretamente conectado pela interface física, mas sim por uma loopback.

### 4. Qual a função do update-source Loopback1?
Ele faz o R1 usar o IP da sua loopback 11.11.11.11 como origem da sessão BGP com o ISP1.

### 5. Por que foi criada a rota ip route 200.18.245.64 255.255.255.224 Null0?
Porque o BGP só anuncia uma rede pelo comando network se essa rede já existir na tabela de roteamento, ea rota para Null0 cria essa entrada e permite anunciar o prefixo 200.18.245.64/27.

### 6. Qual a diferença entre o pareamento com o ISP1 e com o ISP2?
Com o ISP1, o BGP é feito usando loopbacks, então precisa dos comandos ```update-source```, ```ebgp-multihop``` e as rotas estáticas para alcançar a loopback. E com o ISP2, o BGP é feito diretamente pelo IP da interface física.

