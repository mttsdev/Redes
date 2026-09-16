# Máscaras de rede e CIDR

Esses dois conceitos ajudam a entender o **IPv4**, principalmente para determinar **qual parte do endereço representa a rede e qual parte representa os hosts**.

Eles também são importantes para o SOC porque ajudam o analista a entender se dois IPs pertencem à **mesma rede** ou a redes diferentes.

---

## 1. Máscara de rede

A **máscara de rede** indica quais bits de um endereço IPv4 pertencem à **rede** e quais ficam disponíveis para identificar os **hosts** daquela rede.

Por exemplo:

```text
IP:       192.168.1.10
Máscara:  255.255.255.0
```

Podemos visualizar de forma simplificada:

```text
192.168.1 | 10
   REDE   | HOST
```

Nesse caso:

```text
Rede: 192.168.1.0
Host: 10
```

Portanto, o endereço `192.168.1.10` pertence à rede `192.168.1.0`.

---

## 2. O que é CIDR?

**CIDR (Classless Inter-Domain Routing)** é uma forma compacta de representar uma máscara de rede.

Por exemplo:

```text
Máscara:
255.255.255.0
```

pode ser representada como:

```text
/24
```

Então:

```text
IP:       192.168.1.10
Máscara:  255.255.255.0
CIDR:     /24
```

Também podemos escrever:

```text
192.168.1.10/24
```

---

## 3. O que significa `/24`?

Um endereço IPv4 possui **32 bits**.

Quando vemos:

```text
/24
```

isso significa que os **primeiros 24 bits** pertencem à parte da rede.

Os **8 bits restantes** ficam para a parte dos hosts.

```text
11111111.11111111.11111111.00000000
└──────────── 24 ─────────┘└─ 8 ─┘
         REDE                 HOST
```

Convertendo os bits para decimal:

```text
11111111 = 255
00000000 = 0
```

Temos:

```text
255.255.255.0
```

Portanto:

```text
192.168.1.10/24
```

pode ser entendido como:

```text
Rede → 192.168.1
Host → 10
```

A rede completa é:

```text
192.168.1.0/24
```

---

## 4. Por que isso importa?

O computador precisa determinar se determinado destino está na **mesma rede local** ou em **outra rede**.

Por exemplo:

```text
PC A: 192.168.1.10/24
PC B: 192.168.1.20/24
```

Os dois pertencem à:

```text
192.168.1.0/24
```

Portanto, estão na mesma rede.

Agora:

```text
PC A: 192.168.1.10/24
PC B: 192.168.2.10/24
```

Temos:

```text
PC A → 192.168.1.0/24
PC B → 192.168.2.0/24
```

São redes diferentes.

Nesse segundo caso, para que os dispositivos se comuniquem, o tráfego precisará ser encaminhado por um **roteador/gateway**.

---

## 5. Quanto maior o CIDR, menor a rede

Uma regra útil para memorizar:

> **Quanto maior o número depois da `/`, menor é a quantidade de endereços disponíveis na rede.**

Por exemplo:

```text
/16
↓
16 bits para rede
16 bits para hosts
```

Enquanto:

```text
/24
↓
24 bits para rede
8 bits para hosts
```

Podemos visualizar:

```text
/16
11111111.11111111.00000000.00000000
└──── 16 ────┘└──── 16 ────┘


/24
11111111.11111111.11111111.00000000
└──────── 24 ────────┘└─ 8 ─┘
```

Portanto:

```text
/16 → rede maior
/24 → rede menor
```

### Quantidade de endereços

A quantidade total de endereços IPv4 de uma rede pode ser calculada por:

```text
2^(bits de host)
```

Por exemplo:

```text
/24

32 - 24 = 8 bits

2^8 = 256 endereços
```

Em uma subnet IPv4 tradicional, normalmente existem **254 endereços utilizáveis para hosts**, porque o primeiro endereço é reservado para a identificação da rede e o último para broadcast.

```text
192.168.1.0   → endereço da rede
192.168.1.1   → host
...
192.168.1.254 → host
192.168.1.255 → broadcast
```

---

## 6. Exemplos comuns de redes

Alguns exemplos:

```text
10.0.0.0/8
```

É uma rede privada muito grande, com grande quantidade de endereços disponíveis.

```text
172.16.0.0/12
```

Também pertence ao espaço privado IPv4.

```text
192.168.1.0/24
```

É uma rede privada comum em redes domésticas e pequenas redes locais.

É importante observar que o tamanho da rede não é determinado apenas pelo endereço `10`, `172` ou `192`, mas principalmente pelo **CIDR/máscara utilizado**.

Por exemplo:

```text
10.0.0.0/8
```

e

```text
10.0.0.0/24
```

possuem tamanhos de rede completamente diferentes.

---

## 7. Máscaras e CIDR no SOC

Imagine que o SIEM apresente:

```text
src_ip  = 10.10.20.15
dest_ip = 10.10.20.50
```

E sabemos que a rede é:

```text
10.10.20.0/24
```

Podemos determinar que:

```text
10.10.20.15 → 10.10.20.0/24
10.10.20.50 → 10.10.20.0/24
```

Portanto, os dois IPs estão na **mesma rede**.

Agora imagine:

```text
src_ip  = 10.10.20.15
dest_ip = 10.10.30.50
```

Temos:

```text
10.10.20.15 → 10.10.20.0/24
10.10.30.50 → 10.10.30.0/24
```

Nesse caso, são **redes diferentes**.

Isso pode ajudar o SOC a entender o contexto da comunicação:

```text
src_ip
   ↓
Qual é a rede de origem?
   ↓
dest_ip
   ↓
Qual é a rede de destino?
   ↓
Mesma rede?
   │
   ├── SIM → comunicação local
   │
   └── NÃO → precisa de roteamento
```

---

## 8. Máscara, CIDR e Gateway

Esses conceitos estão diretamente relacionados.

Imagine:

```text
PC
192.168.1.10/24
     │
     │ destino: 192.168.1.50
     ▼
Mesma rede
```

O computador pode realizar a comunicação diretamente.

Agora:

```text
PC
192.168.1.10/24
     │
     │ destino: 10.10.20.50
     ▼
Outra rede
     │
     ▼
Gateway
192.168.1.1
     │
     ▼
Roteamento
```

A máscara/CIDR ajuda o computador a determinar **se o destino está na rede local**.

Se não estiver, o tráfego normalmente é encaminhado para o **gateway padrão**.

---

## Resumo

| Conceito | Função |
|---|---|
| Máscara | Define quais bits pertencem à rede e quais aos hosts |
| CIDR | Representação compacta da máscara |
| `/24` | 24 bits para rede e 8 bits para hosts |
| `/16` | 16 bits para rede e 16 bits para hosts |
| Rede | Identifica o segmento/sub-rede IPv4 |
| Host | Identifica um endereço dentro daquela rede |
| Gateway | É utilizado para encaminhar tráfego para outras redes |

### Regra para lembrar

```text
IPv4
32 bits
   ↓
Máscara/CIDR
   ↓
Parte da rede + parte do host
```

Por exemplo:

```text
192.168.1.10/24

192.168.1 → REDE
10        → HOST
```

E:

```text
/24
 ↓
24 bits de rede
 ↓
8 bits de host
 ↓
256 endereços totais
 ↓
normalmente 254 utilizáveis para hosts
```

No SOC:

```text
src_ip + CIDR
      ↓
Rede de origem

dest_ip + CIDR
      ↓
Rede de destino

      ↓

Mesma rede?
      │
      ├── SIM → comunicação local
      │
      └── NÃO → comunicação entre redes
                    ↓
                 Gateway/Roteador
```

O ponto principal é:

> **A máscara e o CIDR permitem determinar a divisão entre rede e host e ajudam a identificar se dois endereços IPv4 pertencem à mesma rede.**
