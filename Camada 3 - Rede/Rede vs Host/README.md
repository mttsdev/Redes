# Rede vs Host

Em uma rede com vários computadores, precisamos distinguir duas coisas:

```text
Rede → Qual é a "área" ou segmento?
Host → Qual dispositivo/endereço específico está dentro dessa rede?
```

Por exemplo:

```text
Rede: 192.168.1.0/24
```

Podemos ter vários hosts dentro dela:

```text
192.168.1.10
192.168.1.20
192.168.1.30
192.168.1.50
```

Todos pertencem à mesma rede, mas representam endereços diferentes.

---

## 1. Parte de Rede e Parte de Host

A **parte de rede** indica a qual rede o endereço pertence.

A **parte de host** identifica um endereço específico dentro daquela rede.

Por exemplo:

```text
192.168.1.10/24
```

Como estamos utilizando `/24`, temos:

```text
192.168.1 | .10
   REDE   | HOST
```

Portanto:

```text
Rede → 192.168.1.0/24
Host → 192.168.1.10
```

Outros hosts podem existir nessa mesma rede:

```text
192.168.1.10
192.168.1.20
192.168.1.30
192.168.1.50
```

Visualmente:

```text
192.168.1.0/24
      │
      ├── 192.168.1.10
      ├── 192.168.1.20
      ├── 192.168.1.30
      └── 192.168.1.50
```

São **hosts diferentes**, mas pertencem à **mesma rede**.

---

## 2. Quem determina onde termina a rede?

Quem determina a divisão entre **rede e host** é a **máscara de rede/CIDR**.

Compare:

```text
192.168.1.10/24
```

com:

```text
192.168.1.10/16
```

No `/24`:

```text
192.168.1 | .10
   REDE   | HOST
```

No `/16`:

```text
192.168 | .1.10
  REDE  | HOST
```

Portanto, o mesmo endereço IP pode pertencer a uma rede diferente dependendo da máscara utilizada.

A divisão não é simplesmente determinada pelos primeiros números do IP.

A máscara é que informa ao computador:

> "Estes bits pertencem à rede; os restantes identificam os hosts."

---

## 3. Voltando aos bits

Um endereço IPv4 possui:

```text
32 bits
```

Com `/24`:

```text
11111111.11111111.11111111.00000000
└──────────── 24 ─────────┘└── 8 ──┘
            REDE               HOST
```

Temos:

```text
24 bits → rede
8 bits  → host
```

Agora imagine `/26`:

```text
11111111.11111111.11111111.11000000
└────────────── 26 ──────────────┘└6┘
                  REDE             HOST
```

Temos:

```text
26 bits → rede
6 bits  → host
```

Observe o que aconteceu:

```text
/24
24 bits de rede
8 bits de host
```

Depois:

```text
/26
26 bits de rede
6 bits de host
```

Ao aumentar o CIDR:

```text
/24 → /26
```

pegamos **2 bits que antes pertenciam aos hosts e passamos para a parte de rede**.

Por isso:

> Quanto maior o CIDR, menor tende a ser a quantidade de hosts disponíveis em cada rede.

---

## 4. Uma rede pode ser dividida em várias redes menores

Esse conceito é importante.

Imagine:

```text
192.168.1.0/24
```

Temos uma rede com:

```text
256 endereços totais
```

Podemos dividir essa rede em redes menores utilizando um CIDR maior.

Por exemplo:

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

Assim, uma rede `/24` foi dividida em **quatro redes `/26`**.

Visualmente:

```text
192.168.1.0/24
        │
        ├── 192.168.1.0/26
        ├── 192.168.1.64/26
        ├── 192.168.1.128/26
        └── 192.168.1.192/26
```

Isso é chamado de **subnetting**.

Em uma empresa, isso pode ser utilizado para organizar diferentes segmentos:

```text
Rede corporativa
       │
       ├── VLAN/segmento de funcionários
       ├── VLAN/segmento de servidores
       ├── VLAN/segmento de visitantes
       └── VLAN/segmento de dispositivos
```

A divisão exata depende da arquitetura da rede.

---

## 5. Rede vs Host no SOC

Esse conceito é útil porque o SOC frequentemente recebe eventos contendo:

```text
src_ip
dest_ip
```

Por exemplo:

```text
src_ip  = 192.168.10.25
dest_ip = 192.168.10.80
```

Se sabemos que os hosts estão em:

```text
192.168.10.0/24
```

podemos identificar:

```text
Origem  → 192.168.10.0/24
Destino → 192.168.10.0/24
```

Portanto:

```text
Mesma rede
```

Agora imagine:

```text
src_ip  = 192.168.10.25
dest_ip = 192.168.20.80
```

Utilizando `/24`:

```text
Origem:
192.168.10.0/24

Destino:
192.168.20.0/24
```

São redes diferentes.

Nesse caso, a comunicação precisa passar por um dispositivo de camada 3, como um **roteador ou gateway**, para chegar à outra rede.

---

## 6. Por que isso é útil durante uma investigação?

Imagine um alerta:

```text
src_ip  = 10.10.20.50
dest_ip = 10.10.20.60
```

Sabemos que ambos pertencem a:

```text
10.10.20.0/24
```

O SOC pode então investigar:

```text
Host 10.10.20.50
       │
       │ comunicação
       ▼
Host 10.10.20.60
```

Agora imagine:

```text
src_ip  = 10.10.20.50
dest_ip = 10.10.30.60
```

Temos:

```text
10.10.20.0/24
        │
        │ roteamento
        ▼
10.10.30.0/24
```

Isso muda o contexto da investigação.

Podemos perguntar:

```text
Por que esse host está acessando outra rede?
        ↓
Essa comunicação é esperada?
        ↓
O host de origem deveria ter acesso à rede de destino?
        ↓
Qual serviço/porta está sendo acessado?
        ↓
Existem outros hosts sendo acessados?
```

---

## 7. Rede vs Host + Camada 4

Esse conceito fica ainda mais interessante quando começamos a estudar a Camada 4.

Na Camada 3:

```text
IP → identifica o endereço do host
```

Na Camada 4:

```text
Porta → identifica o serviço/processo associado à comunicação
```

Por exemplo:

```text
192.168.10.25:52344
        │      │
        │      └── Porta de origem
        │
        └───────── Host de origem
```

E:

```text
192.168.10.80:443
        │      │
        │      └── Porta de destino
        │
        └───────── Host de destino
```

Podemos interpretar:

```text
192.168.10.25:52344
        │
        │ TCP
        ▼
192.168.10.80:443
```

Nesse caso:

```text
Rede → 192.168.10.0/24
Host de origem → 192.168.10.25
Host de destino → 192.168.10.80
Porta de destino → 443
Serviço → HTTPS
```

Essa é exatamente a combinação que começamos a observar no SOC:

```text
Rede
 ↓
Host
 ↓
IP
 ↓
Porta
 ↓
Serviço
 ↓
Comportamento
```

---

## 8. Como pensar como SOC N1

Ao encontrar uma comunicação entre dois IPs, podemos seguir uma sequência:

```text
Evento
  ↓
Qual é o IP de origem?
  ↓
Qual é a rede de origem?
  ↓
Qual é o IP de destino?
  ↓
Qual é a rede de destino?
  ↓
São da mesma rede?
  ↓
Qual protocolo?
  ↓
Qual porta?
  ↓
Qual serviço?
  ↓
Essa comunicação é esperada?
  ↓
Investigar contexto
```

Por exemplo:

```text
src_ip    = 10.10.20.50
src_port  = 51520

dest_ip   = 10.10.30.80
dest_port = 445

protocol  = TCP
```

Podemos começar entendendo:

```text
Origem:
10.10.20.50
     ↓
Rede 10.10.20.0/24

Destino:
10.10.30.80
     ↓
Rede 10.10.30.0/24

Redes diferentes
     ↓
Comunicação entre redes
     ↓
TCP
     ↓
Porta 445
     ↓
SMB
```

A partir daí, o SOC pode verificar se aquele host deveria estar acessando SMB naquela rede e naquele momento.

Importante:

> O fato de serem redes diferentes ou de existir uma determinada porta não significa, por si só, que existe um ataque.

O contexto da comunicação é que determina o que merece investigação.

---

## Resumo

| Conceito | Função |
|---|---|
| Rede | Identifica o segmento/sub-rede ao qual os endereços pertencem |
| Host | Identifica um endereço específico dentro da rede |
| Máscara/CIDR | Determina a divisão entre rede e host |
| `/24` | 24 bits de rede e 8 bits de host |
| `/26` | 26 bits de rede e 6 bits de host |
| Subnetting | Divisão de uma rede em redes menores |
| Mesma rede | Hosts pertencem à mesma sub-rede |
| Redes diferentes | Comunicação normalmente exige roteamento |

### Regra para lembrar

```text
IPv4
   ↓
Máscara/CIDR
   ↓
┌───────────────┬──────────────┐
│     REDE      │     HOST     │
└───────────────┴──────────────┘
```

Por exemplo:

```text
192.168.1.10/24

192.168.1 → REDE
10        → HOST
```

E no SOC:

```text
src_ip
   ↓
Rede de origem
   ↓
dest_ip
   ↓
Rede de destino
   ↓
Mesma rede?
   │
   ├── SIM → comunicação dentro da mesma sub-rede
   │
   └── NÃO → comunicação entre redes
                ↓
             roteamento
```

Quando adicionamos a Camada 4:

```text
IP   → qual host?
Porta → qual serviço?
```

Portanto:

> **Rede identifica o segmento, host identifica o endereço específico dentro dele, e o CIDR/máscara determina onde termina a parte de rede e começa a parte de host.**
