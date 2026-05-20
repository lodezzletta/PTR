# Laboratorio 5 - Enzo porto e Mello - 221006727
## Objetivo
Configurar e validar o roteamento dinâmico em uma topologia com três roteadores, comparando o funcionamento dos protocolos RIP e OSPF em um mesmo cenário.

## Topologia
<img width="696" height="790" alt="image" src="https://github.com/user-attachments/assets/a68dac51-237d-480c-ae0b-3603480da95e" />

## Funcionamento do RIP
```
Router-XX> enable
Router-XX# configure terminal
Router-XX(config)# router rip
Router-XX(config-router)# network 172.16.0.0
Router-XX(config-router)# end
```
### Router-RJ
<img width="399" height="310" alt="image" src="https://github.com/user-attachments/assets/1ec3b4af-c1c9-439f-a893-3ffbb15b3a2e" />.

<img width="349" height="334" alt="image" src="https://github.com/user-attachments/assets/79951749-75f7-447c-89d3-f14fc2b04c99" />.



### Router-SP
<img width="564" height="421" alt="image" src="https://github.com/user-attachments/assets/16d4d874-0ba7-421f-a5f7-16a2b1623996" />.

<img width="513" height="467" alt="image" src="https://github.com/user-attachments/assets/987a5598-1add-4ceb-bb93-ed8c62011b49" />.


### Router-BH 
<img width="577" height="421" alt="image" src="https://github.com/user-attachments/assets/4eaa48b6-8f80-4042-88e9-3d793b625e99" />.

<img width="498" height="438" alt="image" src="https://github.com/user-attachments/assets/76a64240-0712-4167-8ce9-b8b94bbcec8c" />.

Codigos utilizados para a configuração do RIP em seus respectivos roteadores.

### Teste
<img width="590" height="742" alt="image" src="https://github.com/user-attachments/assets/db0b3d10-84cd-4715-8a67-093cc7489fdd" />

## Funcionamento do OSPF
```
Router-XX> enable
Router-XX# configure terminal
Router-XX(config)# router ospf 64
Router-XX(config-router)# network 172.16.XX.0 0.0.0.255 area 0
Router-XX(config-router)# network 172.16.XXX.0 0.0.0.255 area 0
Router-XX(config-router)# end
```

### Router-RJ
<img width="454" height="213" alt="image" src="https://github.com/user-attachments/assets/2009f06f-69d8-48db-8bbb-8d11f2e708dd" />.

<img width="588" height="106" alt="image" src="https://github.com/user-attachments/assets/88289495-9ffd-4a84-8034-72af6e56eb7e" />.

<img width="574" height="328" alt="image" src="https://github.com/user-attachments/assets/6a2076b5-b529-465f-92f2-87b0940fd7eb" />.

### Router-SP
<img width="443" height="227" alt="image" src="https://github.com/user-attachments/assets/80d87cbe-630d-41eb-a274-01df19eb4095" />.

<img width="541" height="94" alt="image" src="https://github.com/user-attachments/assets/99bea713-2274-4421-983c-c05e0253902f" />.

<img width="566" height="345" alt="image" src="https://github.com/user-attachments/assets/2a55168f-0385-4318-9572-710b8c88c48a" />.


### Router-BH 
<img width="444" height="197" alt="image" src="https://github.com/user-attachments/assets/04ec78b9-29b1-4e55-ba28-d844d5921072" />.

<img width="564" height="98" alt="image" src="https://github.com/user-attachments/assets/e9148399-a85e-4020-8489-d65e36f648ca" />.

<img width="552" height="324" alt="image" src="https://github.com/user-attachments/assets/a0b4905e-ff32-43d5-9c5b-f0b43b7e0a50" />.

codigos usados para a configuração do OSPF em seus respectivos roteadores.

### Teste
<img width="595" height="774" alt="image" src="https://github.com/user-attachments/assets/d2e9fe16-72c8-46be-aac3-a2974a4f2d55" />



## Análise comparativa RIP x OSPF
### 1. Qual protocolo foi mais simples de configurar?
O RIP foi mais simples, já que precisa de menos comandos e a configuração é mais direta. no qual so ativar o RIP e informar quais redes vão participar do roteamento.
E o OSPF exige mais atenção, porque usa áreas, identificação de roteadores e outras coisas.

### 2. Qual protocolo apresentou maior riqueza de informações operacionais?
O OSPF apresentou mais informações operacionais, já que você pode visualizar os vizinhos, estados dos adjacêntes, as áreas, etc.
E o RIP mostra informações mais simples, principalmente as rotas aprendidas e a quantidade de saltos até cada destino.

### 3. Qual a principal métrica do RIP?
A principal métrica do RIP é escolher o caminho com a menor quantidade de saltos, ou seja, o número de roteadores até chegar à rede de destino.

### 4. Qual algoritmo é usado pelo OSPF?
O algoritmo usado no OSPF é o SPF, que calcula o melhor caminho com base no custo dos enlaces.

### 5. Qual protocolo tende a escalar melhor?
O OSPF se escala melhor, visto que foi projetado para redes maiores, permite divisão em áreas e trabalha com informações mais completas da topologia.
E o RIP é mais indicado para redes pequenas, porque tem limite de saltos e pode ficar lento ou pouco eficiente em redes maiores.

### 6. Qual protocolo converge melhor em cenários maiores?
O OSPF converge melhor em cenários maiores, pois atualiza as rotas mais rapidamente quando ocorre uma mudança na rede.
E o RIP converge mais devagar, pois depende de atualizações periódicas e trabalha de forma mais simples, enviando tabelas de roteamento entre os roteadores.













