# Flags TCP

## 1. O que são Flags TCP?

As Flags TCP são importantes porque aparecem nos eventos de rede e ajudam a entender em que situação uma conexão TCP está. 
Uma flag é, basicamente, um indicador dentro do segmento TCP que informa algo sobre o estado ou a finalidade daquela comunicação.

Essa são as flags TCP:

* SYN → iniciar conexão
* ACK → confirma recebimento
* FIN → encerrar conexão normalmente
* RST → interromper/resetar conexão

## 2. SYN

Significa que o cliente está tentando iniciar uma conexão TCP.

```text
Cliente → Servidor
        SYN
```
## 3. ACK

ACK significa acknowledgment. Ele indica que determinado segmento foi recebido e reconhecido.

No handshake: SYN → SYN-ACK → ACK

Aqui ele confirma o recebimento do SYN-ACK.
Ele talbém é utilizado durante a comunicação normal do TCP para confirmar o recebimento d dados
