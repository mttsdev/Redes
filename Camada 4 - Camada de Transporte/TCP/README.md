# TCP

## 1. O que é TCP?

O **TCP(Transmission Control Protocol)** é um protocolo de transporte orientado à conexão e foi projetado para enviar dados de forma confiável e ordenada. 

Imagine o seguinte:
```text

Cliente                           Servidor
10.0.0.15                         10.0.0.20
     │                               │
     │────── estabelecer conexão ──► │
     │                               │
     │◄────── confirmar ──────────── │
     │                               │
     │────── confirmar ─────────────►│
     │                               │
     │══════ transferência ═════════►│
```

Antes de começar a fazer a transmissão, TCP faz uma conexão chamada de Three Way Handshake.

## 2. Three Way Handshake

Ele utiliza três mensagens:
* SYN: Onde o cliente para o servidor responde: "Quero estabelecer uma conexão"
* SYN -> ACK: O servidor responde: "Recebi. Também aceito."
* ACK: o cliente finaliza com: "Confirmado!"

Essa sequência mostra que uma conexão TCP foi estabelecida. Mas, nem sempre ela termina com ACK, veja este exemplo:

```text
10.0.0.15 → 10.0.0.20:22  SYN
```

Significa que houve uma tentativa de iniciar uma conexão.

Já algo como:

```text
10.0.0.15 → 10.0.0.20:22
SYN
SYN
SYN
SYN
SYN
```

Indica que um cliente está tentando repetidamente estabelecer uma conexão que não está sendo concluída. Isso pode ter várias explicações como firewall, serviço indisponível, problema de rede ou comportamento suspeito.

## 3. Flags TCP

Também temos as Flags TCP, e essas são as mais principais:

```text
SYN   → inicia uma conexão
ACK   → confirma recebimento
FIN   → encerra uma conexão normalmente
RST   → interrompe/reseta uma conexão
```

