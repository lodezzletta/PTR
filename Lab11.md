
# Laboratorio 11A - Enzo Porto e Mello - 221006727

## Objetivo

Implementar um Web Application Firewall (WAF) em uma máquina Linux no PNetLab, utilizando Nginx + ModSecurity + OWASP Core Rule Set (CRS) em modo de proxy reverso, de forma que o tráfego HTTP entre um cliente e um servidor web interno seja inspecionado e controlado na camada de aplicação.

Nginx: servidor web e proxy reverso que recebe as requisições dos clientes e as encaminha para o servidor interno.
ModSecurity: mecanismo de firewall de aplicação que inspeciona requisições e respostas HTTP em busca de padrões suspeitos.
OWASP Core Rule Set (CRS): conjunto de regras prontas para o ModSecurity, criado para detectar e bloquear ataques web comuns, como SQL Injection e XSS.
Ao final deste laboratório, o estudante deverá ser capaz de:

compreender a função de um WAF;
diferenciar WAF de firewall de pacotes e firewall stateful;
implantar um proxy reverso com inspeção HTTP;
validar acesso legítimo a uma aplicação web;
observar o ponto de inserção do WAF na arquitetura da rede.

## Prints

* topologia

<img width="761" height="428" alt="image" src="https://github.com/user-attachments/assets/7ea59d52-450f-4962-9a02-9794edaa0a76" />

* configuração IP do Linux Cliente, do Linux WAF e do Servidor Web Interno


<img width="979" height="503" alt="image" src="https://github.com/user-attachments/assets/29236787-75ab-42c2-85d3-e698af024948" />
<img width="879" height="456" alt="image" src="https://github.com/user-attachments/assets/b9060942-2f55-42f5-9044-b036b6375953" />
<img width="881" height="459" alt="image" src="https://github.com/user-attachments/assets/44c24235-ec75-4981-ab1e-15976c376007" />


* teste do `nginx -t` no WAF

<img width="975" height="122" alt="image" src="https://github.com/user-attachments/assets/9aa5a87b-6614-4351-a999-a6908dc34a42" />

* acesso do cliente ao servidor via WAF

<img width="542" height="87" alt="image" src="https://github.com/user-attachments/assets/03a58145-fc4c-4365-bfb1-08a2b266a9c0" />


* log do Nginx

<img width="975" height="303" alt="image" src="https://github.com/user-attachments/assets/80649e7f-7f2c-4d46-a485-91a87e06e012" />

* log do ModSecurity

<img width="981" height="464" alt="image" src="https://github.com/user-attachments/assets/4d61cc36-45ff-47e5-a352-46539de95360" />

## Configurações realizadas

No Servidor Web Interno, foi instalado o Nginx e criada uma página simples em HTML:

```bash
echo "<h1>Servidor Web interno OK</h1>" > /var/www/html/index.html
```

Depois foi feito o teste local:

```bash
curl http://127.0.0.1
```

O resultado mostrou:

```html
<h1>Servidor Web interno OK</h1>
```

No Linux WAF, foi instalado o Nginx, o módulo do ModSecurity e o OWASP CRS:

```bash
apt install -y nginx
apt install -y libnginx-mod-http-modsecurity modsecurity-crs
```

Também foi criado o arquivo de configuração do ModSecurity em:

```bash
/etc/modsecurity/modsecurity.conf
```

Com o conteúdo principal:

```bash
SecRuleEngine On
SecRequestBodyAccess On
SecResponseBodyAccess Off
SecAuditEngine RelevantOnly
SecAuditLog /var/log/modsec_audit.log
SecTmpDir /tmp/
SecDataDir /tmp/
```

Depois foi criado o arquivo de regras usado pelo Nginx:

```bash
/etc/nginx/modsecurity.conf
```

Com os includes do ModSecurity e do CRS:

```bash
Include /etc/modsecurity/modsecurity.conf
Include /etc/modsecurity/crs/crs-setup.conf
Include /usr/share/modsecurity-crs/rules/*.conf
```

No arquivo padrão do Nginx no WAF, o servidor foi configurado como proxy reverso:

```nginx
server {
    listen 80;
    server_name _;

    modsecurity on;
    modsecurity_rules_file /etc/nginx/modsecurity.conf;

    location / {
        proxy_pass http://192.168.20.10;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

Depois disso, foi feito o teste:

```bash
nginx -t
```

O resultado foi bem-sucedido, mostrando que a configuração estava correta e que o ModSecurity carregou as regras.

## Fluxo Cliente → WAF → Servidor Web

O linuxcliente acessa:

```bash
curl http://192.168.10.1
```

Esse é o IP do WAF. Então o cliente não acessa diretamente o servidor web interno.

O WAF recebe a requisição HTTP, analisa com o ModSecurity e o OWASP CRS, e depois encaminha para o servidor web interno em:

```bash
192.168.20.10
```

O servidor interno responde a requisição, e o WAF devolve a resposta para o cliente.

O teste funcionou quando o cliente recebeu:

```html
<h1>Servidor Web interno OK</h1>
```

Isso mostra que o proxy reverso estava funcionando corretamente.


## Diferença entre firewall tradicional, stateful e WAF

O firewall tradicional analisa pacotes usando informações da rede, como IP de origem, IP de destino, protocolo e porta. Ele não entende o conteúdo da aplicação, então ele pode permitir uma conexão HTTP sem saber se dentro dela existe uma requisição suspeita.

O firewall stateful melhora isso porque acompanha o estado das conexões. Assim, ele consegue saber se um pacote está iniciando uma conexão nova ou se é apenas resposta de uma conexão que já foi permitida.

Já o WAF trabalha em uma camada mais alta. Ele analisa o conteúdo HTTP, como URL, parâmetros, cabeçalhos e corpo da requisição. Por isso, ele é mais adequado para proteger aplicações web, porque consegue identificar padrões de ataque que um firewall comum não conseguiria ver.

## Questões

1. O que caracteriza um WAF?

Um WAF é um firewall de aplicação web, que analisa o tráfego HTTP/HTTPS e verifica o conteúdo das requisições antes de elas chegarem ao servidor web. 

2. Qual é a principal diferença entre um WAF e um firewall stateful?

O firewall stateful acompanha o estado das conexões. 
Já o WAF analisa o conteúdo da aplicação web, como parâmetros HTTP e padrões de ataque.

3. Por que o WAF é mais adequado para proteger aplicações web do que um firewall tradicional?

Porque um firewall tradicional pode apenas liberar a porta 80, mas o WAF consegue analisar se a requisição possui algo suspeito.

4. Qual é a função do ModSecurity neste laboratório?

O ModSecurity foi usado como o mecanismo de WAF, no qual inspeciona as requisições HTTP recebidas pelo Nginx e aplica as regras de segurança configuradas.

5. Qual é a função do OWASP CRS?

O OWASP CRS fornece um conjunto de regras prontas para o ModSecurity, que ajudaa detectar ataques comuns contra aplicações web.

6. O que significa operar o WAF como proxy reverso?

Significa que o cliente não acessa diretamente o servidor web interno. O cliente acessa o WAF, e o WAF encaminha a requisição para o servidor interno. Assim, o WAF fica na frente do serviço web e pode analisar o tráfego antes de repassar.

7. Por que o acesso ao backend deve preferencialmente ocorrer por meio do WAF?

Porque o backend fica menos exposto. Se o cliente acessa o serviço pelo WAF, as requisições passam por uma camada de inspeção antes de chegar ao servidor interno, que ajuda a proteger a aplicação contra requisições maliciosas.

8. Que vantagens práticas surgem ao separar cliente, WAF e servidor web em redes distintas?

A principal vantagem é deixar a rede mais organizada e segura. O cliente fica em uma rede, o servidor web interno fica em outra, e o WAF fica no meio controlando o acesso. 

9. O WAF substitui a necessidade de firewall de rede? Explique.

Não. O WAF não substitui o firewall de rede. Ele complementa a segurança. O firewall de rede continua sendo importante para controlar IPs, portas e protocolos. O WAF adiciona uma proteção na camada da aplicação, analisando o conteúdo das requisições HTTP.

10. Qual a principal evolução conceitual entre os Labs 10, 10B e 11A?

A principal evolução foi sair de uma proteção mais simples para uma proteção mais específica de aplicação. No Lab 10, o firewall filtrava pacotes. No Lab 10B, ele passou a acompanhar o estado das conexões. No Lab 11A, o WAF passou a analisar o conteúdo HTTP, protegendo a aplicação web em uma camada mais alta.
| Laboratório | Tipo de proteção    | Como funciona                                                       | Exemplo de decisão                                                    |
| ----------- | ------------------- | ------------------------------------------------------------------- | --------------------------------------------------------------------- |
| Lab 10      | Firewall de pacotes | Analisa pacotes de forma isolada, usando IP, protocolo e porta.     | Permitir ou bloquear ICMP, HTTP ou Telnet.                            |
| Lab 10B     | Firewall stateful   | Analisa o estado da conexão, como `NEW`, `ESTABLISHED` e `RELATED`. | Permitir o retorno automático de uma conexão já autorizada.           |
| Lab 11A     | WAF                 | Analisa o conteúdo HTTP da requisição web.                          | Bloquear padrões suspeitos como SQL Injection, XSS ou path traversal. |

## Conclusão

O laboratório mostrou como um WAF pode ser usado para proteger uma aplicação web interna. O cliente acessou o serviço usando o IP do WAF, e o WAF encaminhou a requisição para o servidor web interno usando proxy reverso.

Com o ModSecurity e o OWASP CRS ativados, o WAF passa a funcionar como uma camada extra de proteção, analisando o conteúdo das requisições HTTP. Isso é diferente dos laboratórios anteriores, porque agora a proteção não depende apenas de IP, porta ou estado da conexão, mas também do conteúdo da aplicação.
