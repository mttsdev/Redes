# IPv6

O **IPv6 (Internet Protocol version 6)** foi desenvolvido principalmente para solucionar a limitação de endereços do IPv4.

O IPv4 utiliza **32 bits**, permitindo aproximadamente **4,3 bilhões de endereços**. Com o crescimento da Internet e da quantidade de dispositivos conectados, esse espaço de endereçamento tornou-se insuficiente.

O IPv6 utiliza **128 bits**, proporcionando uma quantidade extremamente maior de endereços.

---

## 1. IPv4 x IPv6

Um endereço IPv4 pode aparecer assim:

```text
192.168.1.10
```

Um endereço IPv6 pode aparecer assim:

```text
2001:0db8:85a3:0000:0000:8a2e:0370:7334
```

As principais diferenças são:

```text
IPv4 → 32 bits → 4 octetos → decimal
IPv6 → 128 bits → 8 grupos de 16 bits → hexadecimal
```

No IPv6, cada grupo de 16 bits é representado por **4 dígitos hexadecimais**.

Esses grupos são frequentemente chamados de **hextetos**.

Exemplo:

```text
2001 : 0db8 : 85a3 : 0000 : 0000 : 8a2e : 0370 : 7334
  ↑      ↑      ↑      ↑      ↑      ↑      ↑      ↑
 grupo  grupo  grupo  grupo  grupo  grupo  grupo  grupo
```

Uma comparação simples:

```text
IPv4 → 192.168.1.10
         ↓
      decimal

IPv6 → 2001:db8::1
         ↓
     hexadecimal
```

Outra diferença importante é que o IPv6 **não possui broadcast**. Em situações em que vários dispositivos precisam receber uma comunicação, o IPv6 utiliza principalmente **multicast**.

---

## 2. Abreviação do IPv6

Um endereço IPv6 pode ser bastante longo. Por isso, existem regras para representá-lo de forma abreviada.

Por exemplo:

```text
2001:0db8:0000:0000:0000:0000:0000:0001
```

pode ser representado como:

```text
2001:db8::1
```

Existem duas regras principais.

### Remover zeros à esquerda

Os zeros à esquerda de cada grupo podem ser removidos:

```text
0db8 → db8

0001 → 1
```

Mas não devemos remover todos os zeros de um grupo.

Por exemplo:

```text
0000 → 0
```

### Substituir uma sequência de grupos `0000` por `::`

Uma sequência consecutiva de grupos contendo apenas zeros pode ser substituída por:

```text
::
```

Exemplo:

```text
2001:db8:0000:0000:0000:0000:0000:0001

↓

2001:db8::1
```

Importante:

> `::` pode ser utilizado apenas uma vez em um endereço IPv6.

Isso acontece porque, ao expandir o endereço novamente, precisamos conseguir determinar quantos grupos `0000` estavam representados pelo `::`.

---

## 3. IPv6 não possui broadcast

Diferentemente do IPv4, o IPv6 não utiliza **broadcast**.

Em vez disso, utiliza **multicast** para enviar uma comunicação para vários dispositivos que pertencem a determinado grupo.

De forma simplificada:

```text
IPv4

Broadcast
    ↓
Vários dispositivos
```

Enquanto no IPv6:

```text
IPv6

Multicast
    ↓
Dispositivos pertencentes ao grupo
```

Isso é importante porque o analista SOC pode encontrar tráfego multicast IPv6 e não deve interpretá-lo automaticamente como comportamento suspeito.

---

# 4. Endereços IPv6 especiais

Assim como no IPv4, existem faixas e endereços IPv6 com funções específicas.

## `::1` — Loopback

O endereço:

```text
::1
```

é o **loopback do IPv6**.

Ele representa a própria máquina.

Equivalente ao:

```text
IPv4 → 127.0.0.1
IPv6 → ::1
```

Exemplo:

```text
Computador
    │
    └──► ::1
          │
          └──► Próprio computador
```

É utilizado para testar a própria pilha de rede do dispositivo.

---

## `fe80::/10` — Link-local

Os endereços:

```text
fe80::/10
```

são utilizados para comunicação **no próprio enlace/segmento local**.

Eles são muito comuns em dispositivos IPv6.

Um host pode possuir, por exemplo:

```text
IPv6 global:
2001:db8:1234::50

IPv6 link-local:
fe80::1234:5678
```

O endereço `fe80::/10` não é utilizado para comunicação global pela Internet.

No contexto de SOC, encontrar um endereço `fe80::...` não significa, por si só, que existe algo malicioso. É um tipo normal de endereço IPv6.

---

## `fc00::/7` — Unique Local Address (ULA)

A faixa:

```text
fc00::/7
```

é reservada para **Unique Local Addresses (ULA)**.

Esses endereços são utilizados em redes locais e privadas, de forma semelhante ao conceito de endereços privados do IPv4.

Exemplo:

```text
fc00::10
```

É importante não tratar ULA e IPv4 privado como exatamente a mesma coisa em todos os detalhes, mas ambos podem representar endereçamento utilizado internamente.

---

# 5. IPv6 e dual stack

IPv4 e IPv6 podem funcionar simultaneamente no mesmo dispositivo.

Isso é chamado de **dual stack**.

Por exemplo:

```text
Computador
     │
     ├── IPv4 → 192.168.1.10
     │
     └── IPv6 → 2001:db8:1234::50
```

Assim, o mesmo computador pode realizar uma comunicação utilizando IPv4 e outra utilizando IPv6.

No SOC, isso é importante porque um ativo pode aparecer nos logs utilizando **os dois protocolos**.

---

# 6. IPv6 no SOC

Imagine que o SIEM apresente:

```text
src_ip  = 192.168.1.10
dest_ip = 8.8.8.8
```

Temos um tráfego **IPv4**.

Agora:

```text
src_ip  = 2001:db8:1234::50
dest_ip = 2001:db8:5678::80
```

Temos um tráfego **IPv6**.

O analista SOC precisa reconhecer o protocolo para interpretar corretamente os endereços e investigar o evento.

Algumas perguntas úteis são:

```text
Qual é o IP de origem?
        ↓
É IPv4 ou IPv6?
        ↓
Qual é o IP de destino?
        ↓
O endereço é global, link-local ou ULA?
        ↓
A comunicação era esperada?
        ↓
Qual protocolo da Camada 4 está sendo utilizado?
        ↓
Qual porta/serviço está envolvido?
        ↓
Existem outros eventos relacionados?
```

Por exemplo:

```text
src_ip     = 2001:db8:1234::50
dest_ip    = 2001:db8:5678::80
protocol   = TCP
dest_port  = 443
```

Nesse caso:

```text
IPv6
  ↓
TCP
  ↓
Porta 443
  ↓
HTTPS
```

---

# 7. IPv6 e reconhecimento no SOC

Assim como ocorre com IPv4, o tráfego IPv6 pode fazer parte de atividades legítimas ou de atividades de reconhecimento.

Por exemplo:

```text
Host A
   │
   ├──► Host B
   ├──► Host C
   ├──► Host D
   ├──► Host E
   └──► vários destinos
```

O comportamento isoladamente não é suficiente para concluir que existe um ataque.

O SOC deve observar:

```text
Quem iniciou?
      ↓
Qual é o ativo?
      ↓
Quais foram os destinos?
      ↓
Qual protocolo foi utilizado?
      ↓
Qual frequência?
      ↓
Esse comportamento é esperado?
      ↓
Existem outros eventos relacionados?
```

A investigação deve considerar o contexto do ambiente.

---

# Resumo

| Conceito | IPv4 | IPv6 |
|---|---|---|
| Tamanho | 32 bits | 128 bits |
| Representação | Decimal | Hexadecimal |
| Grupos | 4 octetos | 8 grupos de 16 bits |
| Exemplo | `192.168.1.10` | `2001:db8::1` |
| Broadcast | Possui | Não possui |
| Multicast | Possui | Possui |
| Loopback | `127.0.0.1` | `::1` |
| Endereço local/privado | `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16` | ULA `fc00::/7` |
| Link-local | — | `fe80::/10` |

### Regra para lembrar

```text
IPv4
32 bits
    ↓
4 octetos
    ↓
Decimal
    ↓
192.168.1.10
```

```text
IPv6
128 bits
    ↓
8 grupos de 16 bits
    ↓
Hexadecimal
    ↓
2001:db8::1
```

E para o SOC:

```text
IP no alerta
     ↓
IPv4 ou IPv6?
     ↓
Origem
     ↓
Destino
     ↓
Tipo de endereço
     ↓
TCP/UDP/ICMP...
     ↓
Porta/serviço
     ↓
Contexto
```

O ponto principal é que **IPv6 não é apenas um IPv4 com mais endereços**. Ele possui uma representação diferente e alguns mecanismos de funcionamento diferentes, como a ausência de broadcast e o uso de multicast.
