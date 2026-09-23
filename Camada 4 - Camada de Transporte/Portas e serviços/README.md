# Portas e Serviços

## 1. O que é uma porta?

Na **Camada 3**, o endereço IP ajuda a identificar o **host** que participa da comunicação.

Na **Camada 4**, a **porta** ajuda a identificar o **endpoint de transporte** associado a um serviço ou aplicação dentro daquele host.

Por exemplo:

```text
192.168.1.10:22
```

Podemos interpretar:

```text
192.168.1.10 → endereço IP do host
22            → porta
TCP           → protocolo de transporte
SSH           → serviço normalmente associado à porta 22
```

Assim:

```text
192.168.1.50 → 192.168.1.10:22
```

pode ser interpretado inicialmente como:

> O host `192.168.1.50` está tentando estabelecer uma comunicação TCP com a porta `22` do host `192.168.1.10`, normalmente associada ao serviço SSH.

### Uma observação importante

A porta **não identifica sozinha o processo exato** que está executando no sistema.

Ela identifica um endpoint de transporte, e o sistema operacional associa essa porta a um socket utilizado por uma aplicação ou serviço.

Por isso:

```text
Porta 22 → forte indicação de SSH
```

mas não:

```text
Porta 22 → garantia de SSH
```

---

## 2. Porta não é exatamente "o serviço"

A porta é simplesmente um **número utilizado pela comunicação de transporte**.

Existem associações convencionais entre determinadas portas e determinados serviços.

| Porta | Protocolo | Serviço normalmente associado |
|---:|---|---|
| **20/21** | TCP | FTP |
| **22** | TCP | SSH |
| **23** | TCP | Telnet |
| **25** | TCP | SMTP |
| **53** | TCP/UDP | DNS |
| **80** | TCP | HTTP |
| **110** | TCP | POP3 |
| **143** | TCP | IMAP |
| **443** | TCP | HTTPS |
| **445** | TCP | SMB |
| **3389** | TCP/UDP | RDP |

### Observação sobre FTP

No FTP tradicional:

```text
TCP 21 → canal de controle
TCP 20 → canal de dados, em determinados modos de operação
```

Portanto, não devemos pensar simplesmente que:

```text
20/21 = duas portas independentes do "serviço FTP"
```

O comportamento da porta 20 depende do modo utilizado pelo FTP.

### Observação sobre RDP

O RDP tradicionalmente utiliza:

```text
TCP 3389
```

Mas implementações modernas também podem utilizar:

```text
UDP 3389
```

Portanto, ao investigar RDP, é importante observar também o **protocolo de transporte**.

---

## 3. Uma porta pode ser alterada

O número da porta é uma convenção, não uma regra absoluta.

Por exemplo, o SSH normalmente utiliza:

```text
TCP/22
```

Mas um administrador pode configurar o SSH para utilizar:

```text
TCP/2222
```

Nesse cenário:

```text
2222 → SSH
```

é perfeitamente possível.

Da mesma forma, outro serviço pode ser configurado para utilizar a porta `22`.

Portanto:

```text
Porta 22 ≠ garantia de SSH
Porta 2222 ≠ garantia de outro serviço
```

A porta é uma **pista importante**, mas o contexto pode ser necessário para confirmar o serviço.

---

## 4. Porta de origem e porta de destino

Considere:

```text
192.168.1.50:51543 → 192.168.1.10:443
```

Temos:

```text
IP de origem:       192.168.1.50
Porta de origem:    51543

IP de destino:      192.168.1.10
Porta de destino:   443
```

Podemos visualizar:

```text
Cliente                         Servidor
192.168.1.50                    192.168.1.10
porta 51543                     porta 443
     │                               │
     └────────── TCP ───────────────►│
```

Nesse exemplo:

```text
51543 → porta de origem
443   → porta de destino
```

A porta `51543` é uma **porta efêmera**, normalmente escolhida temporariamente pelo sistema operacional do cliente para aquela comunicação.

A porta `443`, por outro lado, é a porta de destino normalmente associada ao **HTTPS**.

---

## 5. Como uma conexão pode ser identificada

Uma comunicação TCP pode ser diferenciada pelo conjunto de informações presentes em seus endpoints.

Por exemplo:

```text
192.168.1.50:51543 → 192.168.1.10:443
```

Temos:

```text
IP origem
Porta origem
IP destino
Porta destino
Protocolo
```

Esses elementos formam o contexto básico para identificar uma comunicação.

Em uma investigação de rede, podemos encontrar algo como:

```text
SRC=192.168.1.50
SRC_PORT=51543
DEST=10.10.10.20
DST_PORT=443
PROTOCOL=TCP
```

A interpretação inicial seria:

> O host `192.168.1.50`, utilizando a porta efêmera `51543`, estabeleceu ou tentou estabelecer uma comunicação TCP com a porta `443` do host `10.10.10.20`, normalmente associada ao HTTPS.

---

## 6. Portas e serviços no SOC N1

Um endereço IP sozinho muitas vezes não é suficiente para entender o contexto de um evento.

Compare:

```text
192.168.1.50 → 192.168.1.10:443
```

com:

```text
192.168.1.50 → 192.168.1.10:3389
```

O host de destino é o mesmo, mas a **porta de destino é diferente**.

Consequentemente, o serviço normalmente associado também é diferente:

```text
443  → HTTPS
3389 → RDP
```

Isso pode mudar completamente o contexto da investigação.

---

## 7. Exemplo de investigação SOC N1

Imagine que o SIEM mostre:

```text
SRC_IP=192.168.1.50
DEST_IP=10.10.10.20
DST_PORT=3389
PROTOCOL=TCP
```

A primeira interpretação seria:

> O host `192.168.1.50` está tentando estabelecer uma comunicação TCP com a porta `3389` do host `10.10.10.20`, normalmente associada ao RDP.

A partir daí, o analista começa a investigar o contexto.

```text
Esse computador deveria utilizar RDP?

O destino é um servidor?

O usuário normalmente realiza esse tipo de acesso?

A conexão foi permitida ou bloqueada?

O Three-Way Handshake foi concluído?

Houve apenas uma tentativa ou várias?

Houve muitos hosts tentando acessar a porta 3389?

O comportamento ocorre normalmente nesse ambiente?
```

Observe que a porta **não é a conclusão da investigação**.

Ela é um dos primeiros elementos que ajudam o analista a entender **qual tipo de comunicação pode estar acontecendo**.

---

## 8. Portas no contexto de uma investigação

Imagine o seguinte conjunto de eventos:

```text
192.168.1.50 → 10.10.10.20:3389
192.168.1.51 → 10.10.10.20:3389
192.168.1.52 → 10.10.10.20:3389
192.168.1.53 → 10.10.10.20:3389
192.168.1.54 → 10.10.10.20:3389
```

Podemos observar que vários hosts estão tentando acessar a mesma porta.

Isso **não significa automaticamente um ataque**.

O SOC N1 precisa investigar:

```text
Quem são os hosts de origem?
Qual é o papel do host de destino?
Os acessos são esperados?
Quantas tentativas ocorreram?
Em quanto tempo?
As conexões foram estabelecidas?
Existe um padrão semelhante em outros momentos?
```

O mesmo raciocínio pode ser aplicado a outras portas.

---

## 9. Resumo para SOC N1

```text
CAMADA 3
    │
    └── IP → identifica o host

CAMADA 4
    │
    ├── Protocolo → TCP / UDP
    │
    └── Porta → identifica o endpoint de transporte
                  associado a uma comunicação
```

Exemplo:

```text
192.168.1.50:51543
        │
        │ TCP
        ▼
192.168.1.10:443
```

```text
192.168.1.50 → host de origem
51543        → porta efêmera de origem
192.168.1.10 → host de destino
443          → porta de destino
TCP          → protocolo de transporte
HTTPS        → serviço normalmente associado
```

### Regra importante

```text
PORTA ≠ garantia do serviço
```

A porta fornece uma **indicação**.

Para uma investigação SOC N1, o ideal é cruzar:

```text
IP
+
Porta
+
Protocolo
+
Direção da comunicação
+
Volume/frequência
+
Contexto do host
+
Logs adicionais
```

Assim, a porta deixa de ser apenas um número e passa a ser uma informação útil para entender **o que está acontecendo na rede**.
