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
