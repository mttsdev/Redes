# Gateway

## 1. O que é?

Se um computador precisa fazer uma comunicação com outro computador, mas ambos estão em redes distintas, para isso o computador precisa enviar o pacote ao gateway, que geralmente é um roteador ou uma interface de roteador.

Exemplo:

```text
Na mesma rede:
PC A ────> PC B

Em outra rede:
PC A ──> Gateway ──> Outra rede ──> PC B
```

## Gateway Padrão

**Gateway padrão** é o endereço para qual o dispositivo envia tráfego quando o destino não está em uma rede que ele sabe que não consegue alcançar diretamente.

Por exemplo:

```text
IP:              192.168.1.10
Máscara:         255.255.255.0
Gateway padrão:  192.168.1.1
```
```192.168.1.10``` → endereço do computador
```255.255.255.0``` → determina a rede
```192.168.1.1``` → porta de saída para outras redes

Em SOC N1, ele ajuda a responder perguntas como "Como esse tráfego saiu da rede 192.168.1.0/24?", sendo o Gateway parte da resposta
