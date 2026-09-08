# IPv4

O **IPv4 (Internet Protocol version 4)** é uma versão do protocolo IP que utiliza **32 bits** para representar um endereço.

Um endereço IPv4 normalmente é escrito no formato:

```text
192.168.1.10
```

Esses **32 bits** são divididos em **4 octetos**, e cada octeto possui **8 bits**:

```text
192      .      168      .      1      .      10
8 bits          8 bits         8 bits        8 bits
```

Como cada octeto possui 8 bits, seu valor pode variar de **0 a 255**.

Portanto:

```text
Menor endereço: 0.0.0.0
Maior endereço: 255.255.255.255
```

Por isso, um endereço como:

```text
192.168.1.300
```

é **inválido**, pois `300` ultrapassa o limite de `255` de um octeto.

---

## Rede e Hosts

O IPv4 não serve apenas para identificar um dispositivo.

Um endereço IPv4 possui uma parte que representa a **rede** e outra que representa os **hosts** dentro dessa rede.

Por exemplo:

```text
192.168.1.10/24
```

O `/24` indica que **24 bits pertencem à rede**, enquanto os **8 bits restantes pertencem aos hosts**.

```text
192.168.1. | 10
   REDE    | HOST
  24 bits  | 8 bits
```

Assim, dispositivos como:

```text
192.168.1.10
192.168.1.20
192.168.1.30
```

podem pertencer à mesma rede:

```text
192.168.1.0/24
```

Enquanto:

```text
192.168.2.0/24
```

representa outra rede.


Neste caso, **24 bits são destinados à rede** (`192.168.1.`),
e os **8 bits restantes são destinados aos hosts**.

### Exemplo

| Endereço IP | Rede | Host |
|---|---|---|
| `192.168.1.10` | `192.168.1.` | `10` |
| `192.168.1.20` | `192.168.1.` | `20` |
| `192.168.1.30` | `192.168.1.` | `30` |

Todos eles podem estar na **mesma rede**:

> `192.168.1.0/24`

Enquanto:

> `192.168.2.0/24`

representa **outra rede**, pois o endereço da rede mudou de `192.168.1.` para `192.168.2.`.

## IP trabalha sozinho?

Não. Em uma comunicação de rede, o IP trabalha em conjunto com **diversos protocolos**, cada um desempenhando uma função diferente.

Alguns exemplos:

```text
HTTP/HTTPS → comunicação de aplicações
TCP       → transporte e controle da comunicação
IP        → endereçamento e encaminhamento dos pacotes
```

O **IPv4** é utilizado principalmente para realizar o **endereçamento lógico dos dispositivos** e o **encaminhamento dos pacotes entre redes**.

---

## IPv4 Especiais

Existem alguns endereços e faixas de IPv4 que possuem **funções específicas**.

### `127.0.0.1` — Loopback

É o endereço de **loopback**, utilizado para que um dispositivo se comunique consigo mesmo.

```text
127.0.0.1
   ↓
"Este próprio computador"
```

É muito utilizado para testes locais de rede e de serviços.

> Exemplo: acessar `127.0.0.1` significa tentar acessar um serviço hospedado no próprio computador.

---

### `169.254.x.x` — Link-local

É uma faixa de endereços **link-local**.

Ela pode ser atribuída automaticamente a um dispositivo quando ele **não consegue obter um endereço IPv4 por DHCP**, em determinadas situações.

Exemplo:

```text
169.254.10.25
169.254.100.50
```

Esses endereços são utilizados para comunicação **dentro do segmento de rede local** e não são normalmente roteados pela Internet.

---

### `0.0.0.0` — Endereço especial

O significado de `0.0.0.0` depende do contexto em que aparece.

Pode representar, por exemplo:

```text
"Este host / qualquer endereço"
```

ou:

```text
"Todas as interfaces"
```

Também aparece na representação de uma **rota padrão**:

```text
0.0.0.0/0
```

Nesse caso, significa essencialmente:

> "Qualquer destino que não tenha uma rota mais específica."
