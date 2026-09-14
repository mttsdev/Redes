# ICMP

O Protocolo ICMP fica na camada 3 e é usado principalmente para **fazer diagnósticos, controle e comunicação de erros relacionados ao IP**.
O exemplo mais simples é o ```ping```.
Quando fazemos ```ping 192.168.1.20```, ele pergunta se o IP está alcançável.
Uma visualização mais fácil:
```text
PC A                         PC B
192.168.1.10                 192.168.1.20
    |                            |
    | ---- ICMP Echo Request --> |
    |                            |
    | <-- ICMP Echo Reply ------ |
```
O ping, usa dois tipos de mensagens:
* Echo Request → solicitação enviada pelo computador.
* Echo Reply → resposta enviada pelo destino.

Se você recebe a resposta, isso indica que existe conectividade IP entre os pontos e que o destino respondeu ao ICMP. Não significa necessariamente que todos os serviços do computador estejam funcionando.

## 2. ICMP ≠ TCP E UDP

Para não criar uma confusão entre ICMP, TCP e UDP, veja esse exemplo: Ao acessarmos o site: **https://exemplo.com** podemos ter TCP ou UDP envolvidos. Mas já o ```ping 8.8.8.8```. é **ICMP**.

De forma simples:

* TCP/UDP -> Transporte de dados e aplicações
* ICMP -> Teste de conectividade e diagnóstico.

## 3. ICMP para erros

Além de "pingar", podemos imaginar o seguinte:

```text
PC -> Roteador -> Destino
```
O ICMP, dependendo do resultado, pode informar mensagens como:

* **Destination Unreachable**: Destino/recurso não alcançável:
* **Time Exceeded**: O pacote excedeu o limite de saltos.
* **Echo Request/Reply**: utilizado pelo ping.

## 4. ICMP em SOC

Em SOC, pode aparecer assim:

10.10.20.50 ICMP 10.10.20.60

E ter as seguintes perguntas:

Quem é 10.10.20.50 e 10.10.20.60?

Essa comunicação era esperada? 

Houve alguma pings ou milhares de pings?

Um atacante pode usar o ping para descobrir queria mais estão conectadas em uma rede, sendo um reconhecimento de rede.
