# Subnetting

**Subnetting** significa dividir uma rede maior em **redes menores, chamadas sub-redes**.

Imagine a seguinte rede:

```text
192.168.1.0/24
```

Em vez de deixar todos os dispositivos na mesma rede, podemos dividi-la em várias sub-redes.

Por exemplo:

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

Assim, uma rede `/24` foi dividida em **4 sub-redes `/26`**.

---

## 1. Por que fazer subnetting?

Imagine que em uma empresa existam diferentes grupos:

```text
RH
TI
Visitantes
Servidores
```

Podemos dividir a rede para organizar esses grupos:

```text
RH:
192.168.1.0/26

TI:
192.168.1.64/26

Visitantes:
192.168.1.128/26

Servidores:
192.168.1.192/26
```

Visualmente:

```text
192.168.1.0/24
        │
        ├── RH
        │   192.168.1.0/26
        │
        ├── TI
        │   192.168.1.64/26
        │
        ├── Visitantes
        │   192.168.1.128/26
        │
        └── Servidores
            192.168.1.192/26
```

Isso permite:

- organizar melhor os dispositivos;
- separar diferentes segmentos da rede;
- controlar o tráfego entre sub-redes;
- aplicar regras diferentes de segurança;
- reduzir o tamanho dos domínios de broadcast.

É importante lembrar:

> **Subnetting divide uma rede IP em sub-redes menores.**

Em uma rede corporativa, essas sub-redes podem estar associadas a diferentes VLANs, mas **sub-rede IP e VLAN são conceitos diferentes**.

---

## 2. Subnetting no SOC

Para o SOC, essa divisão pode ajudar a identificar **de qual segmento de rede um host faz parte**.

Imagine que um alerta mostre:

```text
src_ip = 192.168.1.70
```

Se a organização utiliza:

```text
TI → 192.168.1.64/26
```

podemos descobrir que:

```text
192.168.1.70
      ↓
192.168.1.64/26
      ↓
Sub-rede de TI
```

Isso fornece contexto para a investigação.

Por exemplo:

```text
src_ip  = 192.168.1.70
dest_ip = 192.168.1.20
```

Podemos identificar:

```text
Origem:
192.168.1.64/26
      ↓
TI

Destino:
192.168.1.0/26
      ↓
RH
```

Então temos:

```text
TI
 │
 │ comunicação entre sub-redes
 ▼
RH
```

O SOC pode investigar se essa comunicação é esperada.

### Uma correção importante

No SIEM, normalmente veremos:

```text
src_ip = 192.168.1.70
```

e não:

```text
src_ip = 192.168.1.70/24
```

O `/24` ou `/26` representa o **prefixo/máscara da rede**, não uma parte que normalmente aparece junto do IP do host no campo `src_ip`.

Podemos ter:

```text
src_ip = 192.168.1.70
rede    = 192.168.1.64/26
```

---

## 3. O que acontece com os hosts?

Em uma rede `/24`:

```text
32 bits totais
```

Temos:

```text
24 bits → rede
8 bits  → hosts
```

Com 8 bits disponíveis para hosts:

```text
2^8 = 256
```

Temos **256 endereços totais**.

Em uma sub-rede IPv4 tradicional, normalmente:

```text
1 endereço → identifica a rede
1 endereço → broadcast
```

Portanto:

```text
256 - 2 = 254
```

Temos **254 endereços utilizáveis por hosts**.

---

## 4. Quantidade de hosts em diferentes CIDRs

A fórmula tradicional para calcular os endereços utilizáveis é:

```text
2^(bits de host) - 2
```

Como IPv4 possui 32 bits:

```text
bits de host = 32 - CIDR
```

Por exemplo:

### /26

```text
32 - 26 = 6 bits de host

2^6 = 64 endereços

64 - 2 = 62 hosts utilizáveis
```

### /25

```text
32 - 25 = 7 bits de host

2^7 = 128 endereços

128 - 2 = 126 hosts utilizáveis
```

### /23

```text
32 - 23 = 9 bits de host

2^9 = 512 endereços

512 - 2 = 510 hosts utilizáveis
```

### /22

```text
32 - 22 = 10 bits de host

2^10 = 1024 endereços

1024 - 2 = 1022 hosts utilizáveis
```

Podemos resumir:

| CIDR | Bits de host | Endereços totais | Hosts utilizáveis |
|---|---:|---:|---:|
| `/22` | 10 | 1024 | 1022 |
| `/23` | 9 | 512 | 510 |
| `/24` | 8 | 256 | 254 |
| `/25` | 7 | 128 | 126 |
| `/26` | 6 | 64 | 62 |

Uma observação: existem casos especiais em IPv4 em que a regra `-2` não se aplica da mesma forma, como `/31` e `/32`. Para o estudo inicial de subnetting tradicional, podemos utilizar a regra `2^host - 2`.

---

## 5. Como funciona a distribuição no /26?

Temos:

```text
192.168.1.0/24
```

Ao dividir em `/26`, teremos:

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

Cada sub-rede possui:

```text
64 endereços totais
62 hosts utilizáveis
```

A primeira sub-rede:

```text
192.168.1.0/26
```

possui:

```text
Rede:
192.168.1.0

Hosts:
192.168.1.1
até
192.168.1.62

Broadcast:
192.168.1.63
```

A segunda:

```text
192.168.1.64/26
```

possui:

```text
Rede:
192.168.1.64

Hosts:
192.168.1.65
até
192.168.1.126

Broadcast:
192.168.1.127
```

A terceira:

```text
192.168.1.128/26
```

possui:

```text
Rede:
192.168.1.128

Hosts:
192.168.1.129
até
192.168.1.190

Broadcast:
192.168.1.191
```

A quarta:

```text
192.168.1.192/26
```

possui:

```text
Rede:
192.168.1.192

Hosts:
192.168.1.193
até
192.168.1.254

Broadcast:
192.168.1.255
```

Visualmente:

```text
192.168.1.0/24
        │
        ├── 192.168.1.0/26
        │   ├── Rede:      .0
        │   ├── Hosts:     .1 → .62
        │   └── Broadcast: .63
        │
        ├── 192.168.1.64/26
        │   ├── Rede:      .64
        │   ├── Hosts:     .65 → .126
        │   └── Broadcast: .127
        │
        ├── 192.168.1.128/26
        │   ├── Rede:      .128
        │   ├── Hosts:     .129 → .190
        │   └── Broadcast: .191
        │
        └── 192.168.1.192/26
            ├── Rede:      .192
            ├── Hosts:     .193 → .254
            └── Broadcast: .255
```

Portanto:

> **O primeiro endereço de cada sub-rede identifica a rede e o último endereço é o broadcast. Os endereços entre eles podem ser utilizados pelos hosts, seguindo as regras do IPv4.**

---

## 6. Como descobrir em qual sub-rede um IP está?

Essa é uma habilidade útil para o SOC.

Imagine:

```text
IP:
192.168.1.70
```

E sabemos que a rede foi dividida em:

```text
/26
```

As sub-redes são:

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

Agora procuramos onde o `.70` se encaixa:

```text
.0 ───────────── .63
.64 ──────────── .127
        ↑
       .70
```

Portanto:

```text
192.168.1.70
      ↓
192.168.1.64/26
```

O host pertence à segunda sub-rede.

---

## 7. Tamanho do bloco

Uma maneira prática de descobrir as sub-redes é calcular o **tamanho do bloco**.

Para `/26`, a máscara é:

```text
255.255.255.192
```

O tamanho do bloco é:

```text
256 - 192 = 64
```

Por isso as redes começam em:

```text
0
64
128
192
```

Ou seja:

```text
192.168.1.0
192.168.1.64
192.168.1.128
192.168.1.192
```

Isso explica por que os valores aumentam de 64 em 64.

---

## 8. Subnetting e roteamento

Aqui temos uma ligação direta com o tópico anterior.

Quando dois hosts estão em sub-redes diferentes:

```text
192.168.1.70
      ↓
192.168.1.64/26
```

e:

```text
192.168.1.20
      ↓
192.168.1.0/26
```

eles pertencem a sub-redes diferentes.

Portanto, a comunicação entre eles normalmente envolve **Camada 3 e roteamento**:

```text
192.168.1.70
      │
      ▼
Gateway
      │
      ▼
Roteamento
      │
      ▼
192.168.1.20
```

Isso conecta os conceitos que estudamos:

```text
Subnetting
    ↓
Sub-redes
    ↓
Hosts pertencem a diferentes segmentos
    ↓
Comunicação entre sub-redes
    ↓
Gateway
    ↓
Roteamento
```

---

## 9. Subnetting + VLAN

Também podemos relacionar subnetting ao que estudamos na Camada 2.

Um ambiente pode ser organizado, por exemplo, assim:

```text
VLAN 10
192.168.10.0/24
Funcionários

VLAN 20
192.168.20.0/24
Visitantes

VLAN 30
192.168.30.0/24
Servidores
```

Nesse exemplo, temos:

```text
VLAN 10
   ↕
Sub-rede 192.168.10.0/24

VLAN 20
   ↕
Sub-rede 192.168.20.0/24

VLAN 30
   ↕
Sub-rede 192.168.30.0/24
```

A associação entre VLAN e sub-rede é uma escolha de arquitetura muito comum, mas não significa que VLAN e sub-rede sejam exatamente a mesma coisa.

Para comunicação entre essas redes, podemos ter:

```text
VLAN 10
    │
    ▼
Roteamento
    │
    ▼
VLAN 20
```

No SOC, isso pode ser relevante porque uma comunicação entre:

```text
192.168.10.50
```

e:

```text
192.168.20.80
```

pode representar tráfego entre segmentos que possuem funções diferentes.

---

## 10. Subnetting no SOC N1

Imagine que o SIEM apresente:

```text
src_ip    = 192.168.1.70
dest_ip   = 192.168.1.20
protocol  = TCP
dest_port = 445
```

Sabemos que a rede utiliza `/26`.

Então:

```text
192.168.1.70
      ↓
192.168.1.64/26
```

e:

```text
192.168.1.20
      ↓
192.168.1.0/26
```

Logo:

```text
Sub-rede de origem
192.168.1.64/26
       │
       │
       ▼
Sub-rede de destino
192.168.1.0/26
```

Agora adicionamos a Camada 4:

```text
TCP
 ↓
445
 ↓
SMB
```

O SOC pode começar a investigar:

```text
O host .70 pertence a qual segmento?
        ↓
O host .20 pertence a qual segmento?
        ↓
Esses segmentos deveriam se comunicar?
        ↓
Por que estão utilizando SMB?
        ↓
Existem outras conexões semelhantes?
        ↓
O comportamento é esperado?
```

O subnetting ajuda a transformar um simples IP em **contexto de rede**.

---

## 11. Anomalias que podem aparecer no SOC

O subnetting não é uma ameaça por si só.

O que pode chamar a atenção é o comportamento dentro ou entre as sub-redes.

Por exemplo:

```text
Host de uma sub-rede
        ↓
Acessando vários hosts
de outra sub-rede
```

Ou:

```text
Host de usuários
        ↓
Tentando acessar
diversos servidores
```

Ou:

```text
Host de uma sub-rede
        ↓
Realizando conexões
para várias portas
em outra sub-rede
```

Esses comportamentos podem justificar investigação.

Por exemplo:

```text
192.168.1.70
      │
      ├──► 192.168.2.10:445
      ├──► 192.168.2.11:445
      ├──► 192.168.2.12:445
      ├──► 192.168.2.13:445
      └──► ...
```

O SOC pode perceber:

```text
Uma máquina
     ↓
Muitos destinos
     ↓
Outra sub-rede
     ↓
Mesmo serviço
     ↓
Investigar contexto
```

Isso pode ser compatível com diferentes situações, como administração legítima, inventário, monitoramento ou atividade suspeita. O comportamento precisa ser correlacionado com outros eventos.

---

## 12. Como pensar como SOC N1

Uma sequência útil:

```text
Evento
  ↓
IP de origem
  ↓
Qual é a sub-rede?
  ↓
IP de destino
  ↓
Qual é a sub-rede?
  ↓
São a mesma sub-rede?
  │
  ├── SIM
  │    ↓
  │  Comunicação local
  │
  └── NÃO
       ↓
    Comunicação entre sub-redes
       ↓
    Roteamento
       ↓
    Protocolo
       ↓
    Porta
       ↓
    Serviço
       ↓
    Comportamento
       ↓
    Investigar
```

Esse raciocínio conecta praticamente tudo que estudamos até agora:

```text
Camada 2
   ↓
MAC / VLAN
   ↓
Camada 3
   ↓
IP / Sub-rede / Gateway / Roteamento
   ↓
Camada 4
   ↓
TCP / UDP / Porta
   ↓
Serviço
   ↓
SOC
   ↓
Comportamento
```

---

## Resumo

| Conceito | Função |
|---|---|
| Subnetting | Divide uma rede maior em redes menores |
| Sub-rede | Uma rede menor criada a partir de uma rede maior |
| `/24` | 256 endereços totais, normalmente 254 utilizáveis |
| `/26` | 64 endereços totais, normalmente 62 utilizáveis |
| Bits de host | `32 - CIDR` |
| Hosts utilizáveis | `2^(bits de host) - 2` em sub-redes IPv4 tradicionais |
| Endereço de rede | Primeiro endereço da sub-rede |
| Broadcast | Último endereço da sub-rede |
| Tamanho do bloco | Ajuda a identificar os limites das sub-redes |
| Gateway | Permite comunicação com outras redes |
| Roteamento | Encaminha tráfego entre redes |

### Regra para lembrar

```text
Rede maior
    ↓
Subnetting
    ↓
Redes menores
    ↓
Cada sub-rede possui:
    │
    ├── Endereço de rede
    ├── Hosts
    └── Broadcast
```

Para `/26`:

```text
192.168.1.0/26
      ↓
Rede:      .0
Hosts:     .1 → .62
Broadcast: .63
```

```text
192.168.1.64/26
      ↓
Rede:      .64
Hosts:     .65 → .126
Broadcast: .127
```

```text
192.168.1.128/26
      ↓
Rede:      .128
Hosts:     .129 → .190
Broadcast: .191
```

```text
192.168.1.192/26
      ↓
Rede:      .192
Hosts:     .193 → .254
Broadcast: .255
```

E para o SOC:

```text
IP
 ↓
Descobrir a sub-rede
 ↓
Identificar o segmento
 ↓
Comparar origem e destino
 ↓
Mesma sub-rede?
 ↓
Se não → roteamento
 ↓
Protocolo
 ↓
Porta
 ↓
Serviço
 ↓
Comportamento
 ↓
Investigar
```

> **Subnetting é a divisão de uma rede maior em sub-redes menores. Para o SOC N1, isso é útil porque permite entender a qual segmento um IP pertence e contextualizar comunicações entre diferentes partes da rede.**
