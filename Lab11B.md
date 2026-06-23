# Laboratorio 11B - Enzo Porto e Mello - 221006727

## Objetivo

Realizar testes de ataques web em um ambiente protegido por WAF, usando Nginx, ModSecurity e OWASP CRS. O objetivo foi verificar como o WAF se comporta diante de requisições HTTP normais e requisições suspeitas, analisando os códigos retornados no Nginx e os eventos registrados nos logs do ModSecurity.

Esse laboratório continua o Lab 11A. No Lab 11A foi feita a configuração do WAF como proxy reverso. Já no Lab 11B, o foco foi testar se o WAF consegue identificar e bloquear padrões suspeitos, como SQL Injection, XSS e tentativa de acesso indevido a arquivos.

## Prints

* topologia

<img width="663" height="442" alt="image" src="https://github.com/user-attachments/assets/431ab39b-fca4-41af-a7e8-c5bd2eba2426" />


* acesso normal ao serviço via WAF

<img width="509" height="45" alt="image" src="https://github.com/user-attachments/assets/6da9ee53-f863-43c7-8237-7bea188ba5e2" />



* teste com parâmetro simples

<img width="603" height="43" alt="image" src="https://github.com/user-attachments/assets/67d09070-5d19-4769-ad4e-2e9b3e9ff89d" />


* teste com User-Agent comum

<img width="704" height="38" alt="image" src="https://github.com/user-attachments/assets/6e2609a0-191c-480c-9a02-87b4605cd31c" />


* teste de SQL Injection

<img width="750" height="168" alt="image" src="https://github.com/user-attachments/assets/81abff25-0e4c-4c5c-bd4a-caed9b4c0158" />


* teste de XSS

<img width="866" height="174" alt="image" src="https://github.com/user-attachments/assets/98d4c1da-6cbf-4e0f-9cef-e4538e9febe4" />


* teste de path traversal

<img width="755" height="168" alt="image" src="https://github.com/user-attachments/assets/ebd39917-bae5-4d1d-9a17-e2c7e662c0a2" />


* teste com URL codificada

<img width="872" height="190" alt="image" src="https://github.com/user-attachments/assets/a05b4a3b-7b91-46de-8616-4c9d0bc12220" />

* logs do Nginx

<img width="1154" height="685" alt="image" src="https://github.com/user-attachments/assets/aa482db0-53ef-4f77-b56a-ee3d83ef10b0" />


* logs do ModSecurity
  
<img width="1237" height="680" alt="image" src="https://github.com/user-attachments/assets/34f54df3-d2d1-4f88-935f-49149e1c2d91" />

<img width="1232" height="678" alt="image" src="https://github.com/user-attachments/assets/e0a354e3-196d-4f03-bc4c-c15e6838daba" />


## Testes realizados

Foram feitos testes de tráfego legítimo e testes de requisições suspeitas. Os testes foram executados no cliente, acessando o IP do WAF:

```bash
192.168.10.1
```

O WAF recebeu as requisições e encaminhou para o servidor web interno quando elas foram permitidas.

## Evidências dos testes

### Acesso normal

Comando usado:

```bash
curl http://192.168.10.1
```

Resultado no log:

```text
GET / HTTP/1.1 200
```

O código `200` mostra que o acesso normal foi permitido.

### Acesso com parâmetro simples

Comando usado:

```bash
curl "http://192.168.10.1/?id=10"
```

Resultado no log:

```text
GET /?id=10 HTTP/1.1 200
```

Esse teste também foi permitido, pois era um parâmetro simples e sem padrão malicioso.

### Acesso com User-Agent comum

Comando usado:

```bash
curl -A "Mozilla/5.0" http://192.168.10.1
```

Resultado no log:

```text
GET / HTTP/1.1 200 "Mozilla/5.0"
```

O WAF não bloqueou esse acesso, pois era uma requisição normal.

### Teste de SQL Injection

Comando usado:

```bash
curl "http://192.168.10.1/?id=1' OR '1'='1"
```

Resultado no log:

```text
GET /?id=1' OR '1'='1 HTTP/1.1 400
```

Nesse caso, o retorno foi `400`, indicando que a requisição foi considerada inválida ou suspeita. O padrão usado lembra uma tentativa de SQL Injection.

### Teste de XSS

Comando usado:

```bash
curl "http://192.168.10.1/?q=<script>alert(1)</script>"
```

Resultado no log:

```text
GET /?q=<script>alert(1)</script> HTTP/1.1 403
```

O código `403` mostra que a requisição foi bloqueada. Esse foi o teste mais claro de atuação do WAF, pois o conteúdo enviado tinha uma tag `<script>`, típica de tentativa de XSS.

No log do ModSecurity apareceu:

```text
XSS Attack Detected via libinjection
```

Também apareceu:

```text
XSS Filter - Category 1: Script Tag Vector
```

Isso mostra que o ModSecurity, usando regras do OWASP CRS, identificou o padrão de ataque XSS.

### Teste de path traversal

Comando usado:

```bash
curl "http://192.168.10.1/../../../etc/passwd"
```

Também foi feito um teste tentando acessar:

```bash
curl "http://192.168.10.1/etc/passwd"
```

Resultado no log:

```text
GET /etc/passwd HTTP/1.1 404
```

Nesse caso, o retorno foi `404`, indicando que o recurso não foi encontrado. Isso não significa falha da rede, apenas que o caminho solicitado não existia como página disponível no servidor web.

### Teste com URL codificada

Comando usado:

```bash
curl "http://192.168.10.1/?file=%2e%2e%2f%2e%2e%2fetc%2fpasswd"
```

Resultado no log:

```text
GET /?file=%2e%2e%2f%2e%2e%2fetc%2fpasswd HTTP/1.1 403
```

Esse teste retornou `403`, indicando bloqueio. Isso mostra que o WAF conseguiu identificar uma tentativa suspeita mesmo com a URL codificada.

## Tabela comparativa entre os modelos de firewall

| Situação                                               | Firewall de pacotes | Firewall stateful | WAF           |
| ------------------------------------------------------ | ------------------- | ----------------- | ------------- |
| Liberar ou bloquear TCP/80                             | Sim                 | Sim               | Sim           |
| Acompanhar conexão já estabelecida                     | Não                 | Sim               | Indiretamente |
| Analisar conteúdo da URL                               | Não                 | Não               | Sim           |
| Identificar padrão de XSS                              | Não                 | Não               | Sim           |
| Identificar padrão de SQL Injection                    | Não                 | Não               | Sim           |
| Bloquear requisição web suspeita sem fechar a porta 80 | Não                 | Não               | Sim           |

## Análise dos logs

O log do Nginx mostrou os acessos feitos pelo cliente `192.168.10.10`. Nele foi possível observar o horário da requisição, o recurso acessado, o código HTTP retornado e o tipo de cliente usado, como `curl/7.58.0` ou `Mozilla/5.0`.

Nos acessos normais, o código retornado foi `200`, mostrando que o serviço estava funcionando corretamente. Isso aconteceu no acesso à página inicial, no teste com `?id=10` e no teste com User-Agent comum.

Nos acessos suspeitos, o comportamento mudou. A tentativa de SQL Injection retornou `400`, o teste de XSS retornou `403`, e a tentativa com URL codificada também retornou `403`. Isso mostra que a rede continuava funcionando, mas algumas requisições foram recusadas por causa do conteúdo HTTP.

O log do ModSecurity confirmou melhor a atuação do WAF. Nele apareceram mensagens relacionadas ao OWASP CRS, como:

```text
XSS Attack Detected via libinjection
```

e:

```text
XSS Filter - Category 1: Script Tag Vector
```

Essas mensagens indicam que o WAF identificou o conteúdo `<script>alert(1)</script>` como uma tentativa de XSS.

## Texto sobre o que foi permitido e o que foi bloqueado

Durante o laboratório, as requisições legítimas foram permitidas normalmente. O acesso à página inicial retornou `200`, o acesso com parâmetro simples `?id=10` também retornou `200`, e o acesso usando User-Agent comum também foi aceito.

Já as requisições suspeitas tiveram outro comportamento. A tentativa de SQL Injection retornou `400`, enquanto o teste de XSS e o teste com URL codificada retornaram `403`. No caso do XSS, o log do ModSecurity mostrou que o OWASP CRS detectou o padrão de ataque.

Isso demonstra a atuação do WAF porque a porta 80 continuou aberta e o serviço web continuou publicado, mas algumas requisições foram bloqueadas com base no conteúdo da aplicação. Ou seja, o bloqueio não aconteceu por IP, porta ou protocolo, mas sim pelo conteúdo HTTP enviado na URL.

## Questões

1. O que diferencia um WAF de um firewall stateful?

Um firewall stateful acompanha o estado das conexões, como conexões novas ou já estabelecidas. Já o WAF analisa o conteúdo da requisição HTTP, como URL, parâmetros e padrões suspeitos. Por isso, o WAF consegue identificar ataques web que um firewall stateful não conseguiria ver.

2. Por que a requisição pode ser bloqueada mesmo com a porta 80 aberta?

Porque a porta 80 estar aberta significa apenas que o serviço HTTP está acessível. Mesmo assim, o WAF pode analisar o conteúdo da requisição e bloquear se encontrar algo suspeito, como `<script>` ou tentativa de acessar arquivos indevidos.

3. O que o WAF consegue observar que o `iptables` não observa?

O WAF consegue observar o conteúdo da aplicação, como parâmetros da URL, cabeçalhos HTTP e padrões de ataque. O `iptables` normalmente trabalha com IP, porta, protocolo e estado da conexão, mas não entende o conteúdo HTTP da mesma forma.

4. Qual foi o comportamento do ambiente diante de tráfego legítimo?

O tráfego legítimo foi permitido. Os acessos normais retornaram código `200`, mostrando que o cliente conseguiu acessar o serviço publicado pelo WAF.

5. Qual foi o comportamento do ambiente diante de tráfego suspeito?

Algumas requisições suspeitas foram bloqueadas ou recusadas. O teste de SQL Injection retornou `400`, o teste de XSS retornou `403`, e o teste de URL codificada também retornou `403`.

6. O que os logs do Nginx mostram?

Os logs do Nginx mostram os acessos recebidos pelo WAF, incluindo IP de origem, horário, método HTTP, recurso acessado, código de resposta e User-Agent. Com isso foi possível ver quais requisições foram aceitas e quais foram bloqueadas.

7. O que os logs do ModSecurity mostram?

Os logs do ModSecurity mostram as regras acionadas pelo WAF. No teste de XSS, o ModSecurity registrou mensagens indicando ataque XSS, como “XSS Attack Detected via libinjection” e “XSS Filter - Category 1: Script Tag Vector”.

8. Um bloqueio HTTP significa necessariamente falha da rede? Explique.

Não. Um bloqueio HTTP não significa que a rede falhou. A rede pode estar funcionando normalmente, com a porta 80 aberta e a conexão TCP estabelecida. O bloqueio acontece porque o WAF analisou o conteúdo da requisição e considerou ele suspeito.

9. O WAF substitui a necessidade de correção de vulnerabilidade na aplicação? Explique.

Não. O WAF ajuda a proteger a aplicação, mas não substitui a correção do problema no código. Ele funciona como uma camada extra de defesa, mas a aplicação ainda precisa ser corrigida para evitar vulnerabilidades reais.

10. Qual foi a principal evidência prática de que o WAF atua na camada de aplicação?

A principal evidência foi o teste de XSS. A porta 80 continuou aberta e o acesso normal funcionou, mas a requisição com `<script>alert(1)</script>` foi bloqueada com código `403`. Além disso, o log do ModSecurity mostrou que uma regra do OWASP CRS detectou o ataque XSS.

## Conclusão

O Lab 11B mostrou que o WAF atua de forma diferente de um firewall tradicional e de um firewall stateful. Enquanto os firewalls anteriores trabalham mais com IP, porta, protocolo e estado da conexão, o WAF analisa o conteúdo da requisição HTTP.

Nos testes realizados, os acessos normais foram permitidos e retornaram `200`. Já alguns acessos suspeitos foram recusados ou bloqueados, como o XSS e a URL codificada, que retornaram `403`.

Com os logs do Nginx foi possível ver os acessos e os códigos HTTP retornados. Com os logs do ModSecurity foi possível confirmar que o OWASP CRS identificou padrões de ataque, principalmente no teste de XSS.

Assim, o laboratório mostrou que é possível manter o serviço web publicado e, ao mesmo tempo, bloquear requisições suspeitas sem fechar a porta 80.
