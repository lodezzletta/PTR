# Laboratório 01 - Configuração inicial no PNetLab

**Aluno:** Enzo Porto e Mello  - 221006727

---
## Topologia do laboratório

<img width="521" height="342" alt="image" src="https://github.com/user-attachments/assets/3d29f0f2-380f-4e31-85c9-8838053757df" />

---

## O enlace físico funciona?

Sim, porque o Host A consegue se comunicar com o roteador na interface da sua própria rede (192.168.10.1). 

## O IP está configurado corretamente?
Sim, os endereços IP foram configurados com o que foi pedido no laboratório: Host A 192.168.10.10/24 e Host B 192.168.20.20/24 e o roteador com uma interface em cada uma dessas redes. 

## Por que o pacote não chega ao Host B?
Porque como Host B está em outra rede (192.168.20.0/24) ainda não foi configurada uma rota default nos hosts. Então o Host A sabe enviar apenas para sua própria rede local, e quando tenta alcançar 192.168.20.10, ele não consegue encaminhar os pacotes para outra rede.

---

## Configuração dos hosts

<img width="897" height="444" alt="image" src="https://github.com/user-attachments/assets/c4386faf-e6d0-463e-a074-46d91b5d1fbb" />
<img width="899" height="86" alt="image" src="https://github.com/user-attachments/assets/90b20f1f-b207-47d6-a8ce-1dc4aee9d79c" />


<img width="897" height="435" alt="image" src="https://github.com/user-attachments/assets/b60f1d55-f3d5-40a7-8447-31202a8d6dc0" />

<img width="905" height="167" alt="image" src="https://github.com/user-attachments/assets/ea9f30e0-c60a-4325-b316-f9c1a1dfca84" />
