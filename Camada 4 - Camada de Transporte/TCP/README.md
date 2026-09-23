# TCP — Transmission Control Protocol

## 1. O que é TCP?

O **TCP (Transmission Control Protocol)** é um protocolo da **Camada 4 (Transporte)**, orientado à conexão e projetado para fornecer uma comunicação **confiável e ordenada** entre os hosts.

Antes da transmissão dos dados, o TCP estabelece uma conexão entre cliente e servidor.

Podemos visualizar de forma simplificada:

```text
Cliente                              Servidor
10.0.0.15                            10.0.0.20
     │                                    │
     │──────────── SYN ──────────────────►│
     │                                    │
     │◄───────── SYN + ACK ───────────────│
     │                                    │
     │──────────── ACK ──────────────────►│
     │                                    │
     │════════════ Dados ════════════════►│
```

Essa sequência de três mensagens é chamada de **Three-Way Handshake**.

O objetivo é estabelecer a conexão e permitir que os dois lados sincronizem informações necessárias para a comunicação TCP.

---

## 2. Three-Way Handshake

O Three-Way Handshake utiliza três segmentos principais:

### 1. SYN

O cliente envia um segmento com a flag **SYN**.

```text
10.0.0.15 ───── SYN ─────► 10.0.0.20:22
```

De forma simplificada:

> "Quero iniciar uma conexão TCP."

---

### 2. SYN + ACK

O servidor responde utilizando duas flags:

```text
10.0.0.20 ───── SYN + ACK ─────► 10.0.0.15
```

De forma simplificada:

> "Recebi sua solicitação e também estou pronto para estabelecer a conexão."

---

### 3. ACK

O cliente responde com **ACK**:

```text
10.0.0.15 ───── ACK ─────► 10.0.0.20:22
```

De forma simplificada:

> "Confirmado."

Após essa sequência, a conexão TCP pode começar a transportar dados.

```text
SYN
  ↓
SYN + ACK
  ↓
ACK
  ↓
CONEXÃO ESTABELECIDA
  ↓
TRANSFERÊNCIA DE DADOS
```

### Visão para o SOC N1

Essa sequência é muito importante durante uma investigação porque permite observar **em que etapa uma comunicação TCP está**.

Por exemplo:

```text
10.0.0.15 → 10.0.0.20:22  SYN
```

Isso mostra uma **tentativa de iniciar uma conexão TCP** com a porta 22.

Por outro lado:

```text
10.0.0.15 → 10.0.0.20:22  SYN
10.0.0.20 → 10.0.0.15:53000 SYN, ACK
10.0.0.15 → 10.0.0.20:22  ACK
```

Mostra que o Three-Way Handshake foi concluído.

---

## 3. E se o Handshake não for concluído?

Nem toda tentativa de conexão TCP chega ao terceiro passo.

Por exemplo:

```text
10.0.0.15 → 10.0.0.20:22  SYN
```

Se não houver resposta, algumas possibilidades incluem:

```text
Firewall bloqueando
Serviço indisponível
Host inacessível
Problema de rede
Porta filtrada
Comportamento suspeito
```

Outro cenário:

```text
10.0.0.15 → 10.0.0.20:22  SYN
10.0.0.15 → 10.0.0.20:22  SYN
10.0.0.15 → 10.0.0.20:22  SYN
10.0.0.15 → 10.0.0.20:22  SYN
```

Isso mostra várias transmissões de **SYN**, mas é importante ter cuidado com a interpretação.

Esses pacotes podem representar **retransmissões do mesmo pedido de conexão**, por exemplo, quando o cliente não recebe a resposta esperada.

Portanto, não devemos concluir automaticamente que existem quatro tentativas independentes.

No SOC, devemos observar também:

```text
IP de origem
IP de destino
Porta de destino
Quantidade de SYN
Intervalo de tempo
Resposta do servidor
Existência de SYN + ACK
Existência de RST
```

Um grande número de SYN pode ter diferentes explicações. Dependendo do padrão observado, pode indicar desde um problema de conectividade até uma atividade de reconhecimento ou outra atividade suspeita.

---

## 4. Flags TCP

As **flags TCP** são campos utilizados para indicar determinadas características e estados da comunicação.

As principais para o estudo de SOC N1 são:

```text
SYN → inicia o estabelecimento de uma conexão

ACK → confirma o recebimento de determinados segmentos

FIN → inicia o encerramento normal de uma conexão

RST → força o encerramento/reset de uma conexão
```

### Exemplo

```text
SYN
```

Pode representar:

> Tentativa de iniciar uma conexão.

```text
SYN + ACK
```

Pode representar:

> O servidor recebeu o SYN e está respondendo à tentativa de conexão.

```text
ACK
```

Pode representar:

> Confirmação de um segmento recebido.

```text
FIN
```

Pode representar:

> Um dos lados está iniciando o encerramento normal da conexão.

```text
RST
```

Pode representar:

> A conexão está sendo rejeitada, interrompida ou resetada.

---

## 5. TCP no SOC N1

Para um analista SOC, não basta apenas identificar que um evento utiliza TCP.

É importante observar **o comportamento da comunicação**.

Por exemplo:

```text
Origem:       10.0.0.15
Destino:      10.0.0.20
Porta destino: 22
Protocolo:    TCP
Flag:         SYN
```

Uma interpretação inicial seria:

> O host `10.0.0.15` tentou iniciar uma conexão TCP com o serviço associado à porta `22` do host `10.0.0.20`.

Depois, podemos verificar:

```text
Houve SYN + ACK?
Houve ACK?
A conexão foi estabelecida?
Houve RST?
Quantas tentativas ocorreram?
Qual foi o intervalo entre elas?
Esse comportamento é normal para esse host?
```

Isso é mais útil para uma triagem do que simplesmente olhar para a porta.

---

## 6. Resumo para SOC N1

```text
TCP
 │
 ├── Camada 4
 ├── Orientado à conexão
 ├── Utiliza Three-Way Handshake
 ├── Oferece controle de entrega
 ├── Mantém ordenação dos dados
 ├── Possui retransmissão
 └── Utiliza flags
```

### Three-Way Handshake

```text
Cliente                     Servidor

   SYN ─────────────────────►
       ◄──────────── SYN+ACK
   ACK ─────────────────────►

   CONEXÃO ESTABELECIDA
```

### Flags fundamentais

```text
SYN → estabelecimento
ACK → confirmação
FIN → encerramento normal
RST → reset/interrupção
```

Para o SOC N1, uma das coisas mais importantes é aprender a olhar para uma comunicação TCP e identificar **se houve apenas uma tentativa, se o handshake foi concluído ou se a conexão foi encerrada/resetada**.