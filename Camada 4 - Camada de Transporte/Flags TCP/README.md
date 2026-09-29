# Flags TCP

## 1. O que são Flags TCP?

As **Flags TCP** são indicadores no cabeçalho TCP que ajudam a identificar a finalidade de cada segmento e o estado da comunicação.

As principais para SOC N1 são:

```text
SYN → inicia uma conexão
ACK → confirma o recebimento
FIN → inicia o encerramento normal
RST → interrompe ou reseta a conexão
```

Existem outras flags, mas essas quatro são as mais importantes para começar.

## 2. SYN

A flag **SYN** é usada para iniciar uma conexão TCP.

```text
10.0.0.15:50000 → 10.0.0.20:22
TCP SYN
```

Interpretação:

> O host `10.0.0.15` está tentando iniciar uma conexão TCP com a porta `22` do host `10.0.0.20`.

No Three-Way Handshake:

```text
SYN
 ↓
SYN-ACK
 ↓
ACK
```

## 3. ACK

**ACK (Acknowledgment)** indica o reconhecimento de informações recebidas.

No handshake:

```text
SYN
SYN-ACK
ACK
```

O `ACK` confirma o recebimento do `SYN-ACK`.

Durante a comunicação normal, também pode ser usado para confirmar o recebimento de dados.

## 4. FIN

**FIN (Finish)** indica que um dos lados não possui mais dados para enviar e está iniciando o encerramento normal da sua parte da conexão.

```text
Cliente → Servidor
FIN
```

Um encerramento normal pode envolver:

```text
FIN
ACK
FIN
ACK
```

Portanto:

```text
FIN → encerramento normal
```

## 5. RST

**RST (Reset)** é usado para interromper uma conexão de forma abrupta.

Exemplo:

```text
Cliente → Servidor:22
SYN

Servidor → Cliente
RST
```

Isso pode ocorrer, por exemplo, quando não existe um serviço aceitando conexões naquela porta.

Outras causas também são possíveis, como conexões inválidas ou interferência de dispositivos de segurança.

```text
SYN → RST
```

Indica que houve uma tentativa de conexão, mas ela foi resetada.

## 6. FIN × RST

```text
FIN → encerramento normal
RST → interrupção/reset abrupto
```

A diferença principal é a forma como a conexão é encerrada.

## 7. Flags TCP no SOC N1

### Handshake completo

```text
10.0.0.15 → 10.0.0.20:22
SYN

10.0.0.20 → 10.0.0.15:50000
SYN-ACK

10.0.0.15 → 10.0.0.20:22
ACK
```

Indica que o **Three-Way Handshake foi concluído**.

### Conexão resetada

```text
10.0.0.15 → 10.0.0.20:22
SYN

10.0.0.20 → 10.0.0.15:50000
RST
```

Indica uma tentativa de conexão seguida de reset.

### Vários SYN

```text
10.0.0.15 → vários IPs:22
SYN
SYN
SYN
SYN
...
```

Pode indicar **reconhecimento ou varredura**, mas é necessário analisar o contexto.

Observe:

```text
Quantidade de destinos
Quantidade de portas
Intervalo de tempo
SYN-ACK e RST recebidos
Comportamento histórico do host
```

Também é importante lembrar que vários `SYN` podem ser **retransmissões da mesma tentativa**, e não necessariamente várias conexões diferentes.

## 8. Resumo

```text
SYN      → iniciar
SYN-ACK  → responder
ACK      → confirmar
FIN      → encerrar normalmente
RST      → resetar/interromper
```

Para o SOC N1:

```text
FLAG + SEQUÊNCIA + CONTEXTO
```

A sequência dos eventos é mais útil do que analisar uma flag isoladamente.