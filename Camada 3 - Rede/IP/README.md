# IP

O **IP (Internet Protocol)** utiliza endereços lógicos para identificar **interfaces de rede** e permitir que os pacotes sejam encaminhados entre diferentes redes.

Por exemplo:

```text
PC 1                         PC 2
192.168.1.10 ─────────────► 192.168.1.20
```

Em um evento de rede, podemos encontrar:

```text
src_ip  = 192.168.1.10
dest_ip = 192.168.1.20
```

Onde:

```text
src_ip  → endereço IP de origem
dest_ip → endereço IP de destino
```

---

## 1. O que é um endereço IP?

O endereço IP é um **endereço lógico** utilizado na comunicação de rede.

Ele permite que os dispositivos sejam identificados e que os roteadores saibam **para qual rede o pacote deve ser encaminhado**.

Uma forma simples de visualizar:

```text
┌──────────────┐
│      PC      │
│ 192.168.1.10 │
└──────┬───────┘
       │
       │ Pacote
       ▼
┌──────────────┐
│   Roteador   │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Outra rede   │
└──────────────┘
```

O IP permite que o tráfego seja encaminhado até a **rede de destino**.

> **Importante:** tecnicamente, um endereço IP é atribuído a uma **interface de rede**. Um computador pode possuir várias interfaces e, consequentemente, vários endereços IP.

---

## 2. IP de origem e IP de destino

Em logs e eventos de rede, é muito comum encontrar:

```text
src_ip  = 192.168.1.10
dest_ip = 192.168.1.20
```

Podemos interpretar:

```text
192.168.1.10
      │
      └──► Quem iniciou/enviou o tráfego


192.168.1.20
      │
      └──► Destino do tráfego
```

Uma representação simples:

```text
┌──────────────┐
│      PC      │
│ 192.168.1.10 │
└──────┬───────┘
       │
       │  src_ip
       │
       ▼
       │
       │  dest_ip
       ▼
┌──────────────┐
│      PC      │
│ 192.168.1.20 │
└──────────────┘
```

No SOC, esses dois campos são fundamentais para entender **quem se comunicou com quem**.

---

## 3. Diferença entre IP e MAC

O **MAC** está relacionado à interface de rede e é utilizado na **Camada 2**.

O **IP** é um endereço lógico utilizado na **Camada 3** para identificar interfaces e permitir o encaminhamento do tráfego entre redes.

```text
┌───────────────────────────────┐
│ Camada 3                      │
│ IP → comunicação entre redes  │
└───────────────────────────────┘

┌───────────────────────────────┐
│ Camada 2                      │
│ MAC → comunicação local       │
└───────────────────────────────┘
```

Uma diferença importante:

```text
MAC → normalmente associado à interface física/virtual
IP  → pode mudar conforme a rede e a configuração
```

Por exemplo, um notebook pode sair de uma rede Wi-Fi e entrar em outra:

```text
Rede A
IP = 192.168.1.10
       │
       ▼
   troca de rede
       │
       ▼
Rede B
IP = 10.10.20.15
```

A interface pode continuar sendo a mesma, mas seu endereço IP pode mudar.

---

## 4. IP público e IP privado

### IP público

Um **IP público** é um endereço utilizado em redes públicas, como a Internet.

Exemplos:

```text
8.8.8.8
1.1.1.1
```

Esses endereços podem ser utilizados para comunicação através da Internet.

---

### IP privado

Um **IP privado** é destinado ao uso em redes privadas, como redes domésticas e corporativas.

Os principais intervalos IPv4 privados são:

```text
10.0.0.0      → 10.255.255.255

172.16.0.0    → 172.31.255.255

192.168.0.0   → 192.168.255.255
```

Em notação CIDR:

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

Exemplos:

```text
10.10.20.50
172.16.5.20
192.168.1.10
```

Todos pertencem a intervalos de endereços IPv4 privados.

---

## 5. IP privado não significa necessariamente "seguro"

No contexto de SOC, é importante não fazer a seguinte associação:

```text
IP privado = seguro
IP público = perigoso
```

Isso está incorreto.

Um computador comprometido pode utilizar um IP privado:

```text
PC comprometido
192.168.10.25
       │
       ▼
Servidor interno
192.168.20.50
```

Essa comunicação é:

```text
IP privado → IP privado
```

Mas pode representar, dependendo do contexto, uma atividade como **movimentação lateral**.

Da mesma forma:

```text
PC interno
192.168.10.25
       │
       ▼
8.8.8.8
```

Uma comunicação com um IP público pode ser completamente legítima.

---

## 6. IP no SOC N1

O endereço IP é um dos primeiros elementos que o analista pode observar em um alerta.

Imagine:

```text
src_ip       = 192.168.10.25
dest_ip      = 185.50.60.70
protocol     = TCP
dest_port    = 443
```

O SOC pode começar a investigação perguntando:

```text
Quem é 192.168.10.25?
        ↓
É um computador, servidor ou outro ativo?
        ↓
Quem é 185.50.60.70?
        ↓
Esse destino é conhecido?
        ↓
A comunicação era esperada?
        ↓
Qual processo iniciou a conexão?
        ↓
Qual porta foi utilizada?
        ↓
Existem outros eventos relacionados?
```

O IP sozinho raramente é suficiente para determinar se uma atividade é maliciosa.

Ele é uma **peça do contexto**.

---

## 7. Resumo

| Conceito | Função |
|---|---|
| IP | Endereço lógico utilizado na comunicação e no encaminhamento de pacotes |
| `src_ip` | IP de origem do tráfego |
| `dest_ip` | IP de destino do tráfego |
| IP privado | Utilizado em redes privadas |
| IP público | Utilizado em redes públicas, como a Internet |
| MAC | Endereço associado à interface de rede, utilizado na Camada 2 |
| IP | Endereço lógico utilizado na Camada 3 |

### IPv4 privado

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

### Regra para lembrar

```text
MAC
 ↓
Camada 2
 ↓
Comunicação local


IP
 ↓
Camada 3
 ↓
Identificação lógica + encaminhamento entre redes
```

E, no SOC:

```text
src_ip + dest_ip
       ↓
Quem falou com quem?
       ↓
Qual protocolo?
       ↓
Qual porta?
       ↓
Qual processo?
       ↓
Esse comportamento é esperado?
```

O ponto principal é:

> **O IP permite identificar logicamente a origem e o destino do tráfego e possibilita que os pacotes sejam encaminhados entre redes.**
