# Roteamento

O **roteamento** é o processo utilizado para determinar **por qual caminho um pacote deve seguir para chegar a uma rede ou destino diferente da rede de origem**.

Ele faz parte da **Camada 3 (Rede)**.

De forma simplificada:

```text
Host
  ↓
Gateway
  ↓
Roteamento
  ↓
Próximo caminho
  ↓
Destino
```

---

## 1. O que é o roteamento?

Imagine:

```text
PC A
192.168.1.10
   |
   v
Gateway
192.168.1.1
   |
   v
Roteador
   |
   +------> Rede 10.0.0.0/24
   |
   +------> Rede 172.16.0.0/16
   |
   +------> Internet
```

O PC `192.168.1.10` quer acessar:

```text
10.0.0.50
```

Primeiro, ele verifica se o destino pertence à sua própria rede.

Se estiver utilizando:

```text
192.168.1.0/24
```

temos:

```text
Origem:
192.168.1.10
      ↓
192.168.1.0/24

Destino:
10.0.0.50
      ↓
10.0.0.0/24
```

São redes diferentes.

Então o computador encaminha o tráfego para seu **gateway padrão**:

```text
192.168.1.10
      |
      v
192.168.1.1
   Gateway
      |
      v
Roteador
      |
      v
10.0.0.50
```

A partir daí, entra em ação o **roteamento**.

---

## 2. Gateway ≠ Roteamento

É importante separar os conceitos.

### Gateway

É o dispositivo ou endereço utilizado pelo host para encaminhar tráfego destinado a **outras redes**.

Por exemplo:

```text
PC
192.168.1.10
      |
      v
Gateway
192.168.1.1
```

### Roteamento

É o processo de **decidir para onde encaminhar o pacote**.

```text
Pacote
  ↓
Roteador
  ↓
Consulta rotas
  ↓
Escolhe caminho
  ↓
Encaminha
```

Um mesmo equipamento pode funcionar como:

```text
Gateway
   +
Roteador
   +
Firewall
   +
NAT
```

Mas são funções diferentes.

---

## 3. Tabela de Roteamento

Para tomar essa decisão, o roteador utiliza uma **tabela de roteamento**.

Um exemplo simplificado:

```text
Destino              Próximo caminho
192.168.1.0/24       rede local
10.0.0.0/24          interface X
172.16.0.0/16        interface Y
0.0.0.0/0            saída padrão
```

Podemos imaginar essa tabela como um conjunto de instruções:

```text
Se destino = 192.168.1.0/24
        ↓
rede local

Se destino = 10.0.0.0/24
        ↓
interface X

Se destino = 172.16.0.0/16
        ↓
interface Y

Se não houver uma rota específica
        ↓
0.0.0.0/0
```

Quando chega um pacote destinado a:

```text
10.0.0.50
```

o roteador procura uma rota que corresponda a:

```text
10.0.0.0/24
```

e então encaminha o pacote pela interface ou próximo salto correspondente.

---

## 4. Próximo salto

Um ponto importante:

> **O roteador normalmente não precisa conhecer o caminho inteiro até o destino.**

Ele precisa saber **qual é o próximo passo**.

Imagine:

```text
PC
 │
 ▼
Roteador A
 │
 ▼
Roteador B
 │
 ▼
Roteador C
 │
 ▼
Servidor
```

O Roteador A pode saber:

```text
Para chegar à rede X
      ↓
Envie para Roteador B
```

Então:

```text
Roteador A
     ↓
"Meu próximo salto é B"
     ↓
Roteador B
```

O Roteador B toma sua própria decisão:

```text
"Para chegar ao destino,
meu próximo salto é C."
```

E assim por diante.

Portanto:

```text
Roteador A
   ↓
próximo salto
   ↓
Roteador B
   ↓
próximo salto
   ↓
Roteador C
   ↓
Destino
```

Cada roteador participa da construção do caminho **tomando decisões de encaminhamento**.

---

## 5. E se não houver uma rota específica?

Pode existir uma **rota padrão**.

Em IPv4, ela é representada por:

```text
0.0.0.0/0
```

Essa rota significa, de forma simplificada:

> "Para destinos que não correspondem a uma rota mais específica, utilize este caminho."

Um exemplo:

```text
Destino              Caminho
192.168.1.0/24       rede local
10.0.0.0/24          interface X
172.16.0.0/16        interface Y
0.0.0.0/0            saída padrão
```

Se o roteador recebe um pacote destinado a:

```text
8.8.8.8
```

e não possui uma rota específica para esse destino, ele pode utilizar:

```text
0.0.0.0/0
```

para encaminhar o tráfego para seu próximo salto padrão.

Isso é muito comum para representar a saída em direção à Internet.

---

## 6. Rota mais específica

Quando existem várias rotas que podem corresponder a um destino, o roteador normalmente escolhe a **rota mais específica**, ou seja, aquela com o maior prefixo/CIDR.

Por exemplo:

```text
10.0.0.0/8
10.10.0.0/16
10.10.20.0/24
0.0.0.0/0
```

Para o destino:

```text
10.10.20.50
```

várias dessas rotas podem ser aplicáveis.

Mas:

```text
10.10.20.0/24
```

é mais específica que:

```text
10.10.0.0/16
```

que é mais específica que:

```text
10.0.0.0/8
```

e todas são mais específicas que:

```text
0.0.0.0/0
```

Uma forma simples de lembrar:

```text
CIDR maior
   ↓
rede mais específica
   ↓
rota mais específica
```

Isso conecta diretamente o que estudamos anteriormente sobre **máscaras e CIDR**.

---

## 7. Roteamento entre redes

Imagine:

```text
Rede A
192.168.1.0/24
      |
      v
   Roteador
      |
      v
Rede B
10.10.20.0/24
```

Um host:

```text
192.168.1.50
```

quer acessar:

```text
10.10.20.80
```

Como as redes são diferentes:

```text
192.168.1.0/24
        ↓
    Roteamento
        ↓
10.10.20.0/24
```

O roteador encaminha o pacote entre essas redes.

Isso é uma das funções fundamentais da Camada 3.

---

## 8. O caminho pode possuir vários roteadores

Em uma rede grande, o caminho pode ser muito maior:

```text
Host
 │
 ▼
Gateway
 │
 ▼
Roteador 1
 │
 ▼
Roteador 2
 │
 ▼
Roteador 3
 │
 ▼
Roteador 4
 │
 ▼
Destino
```

O pacote pode atravessar vários **saltos (hops)** até chegar ao destino.

Por isso, quando utilizamos ferramentas como:

```text
tracert
```

no Windows, ou:

```text
traceroute
```

em sistemas Unix/Linux, podemos observar parte dos roteadores que participam do caminho.

---

## 9. Roteamento no SOC N1

Para o SOC, o importante não é decorar todas as técnicas de roteamento.

O importante é conseguir interpretar a comunicação e entender **se ela está ocorrendo dentro da mesma rede ou atravessando redes diferentes**.

Imagine um evento no SIEM:

```text
src_ip  = 192.168.1.50
dest_ip = 10.20.30.40
```

Podemos raciocinar:

```text
Origem:
192.168.1.50
      ↓
192.168.1.0/24

Destino:
10.20.30.40
      ↓
10.20.30.0/24
```

São redes diferentes.

Então:

```text
Host
 ↓
Gateway
 ↓
Roteamento
 ↓
Outra rede
 ↓
Destino
```

---

## 10. Roteamento e investigação

Imagine que o SIEM mostre:

```text
src_ip    = 10.10.20.50
dest_ip   = 10.10.30.80
protocol  = TCP
dest_port = 445
```

Primeiro podemos entender:

```text
Origem:
10.10.20.50
      ↓
Rede 10.10.20.0/24

Destino:
10.10.30.80
      ↓
Rede 10.10.30.0/24
```

São redes diferentes.

Depois adicionamos a Camada 4:

```text
TCP
 ↓
porta 445
 ↓
SMB
```

Agora temos:

```text
10.10.20.50
     │
     │ roteamento
     ▼
10.10.30.80:445
```

O SOC pode investigar:

```text
O host de origem deveria acessar essa rede?
        ↓
O destino é um servidor conhecido?
        ↓
A porta 445 é esperada nessa comunicação?
        ↓
Existem outras conexões semelhantes?
        ↓
O comportamento é normal para esse host?
```

O roteamento ajuda a entender **como as redes estão relacionadas e por que uma comunicação entre elas pode existir**.

---

## 11. O que pode aparecer no SIEM?

Dependendo da fonte de logs, podemos encontrar informações como:

```text
src_ip
dest_ip
src_port
dest_port
protocol
action
timestamp
bytes
interface
```

Por exemplo:

```text
src_ip     = 192.168.1.50
dest_ip    = 10.20.30.40
src_port   = 52144
dest_port  = 443
protocol   = TCP
action     = allowed
```

O SOC pode interpretar:

```text
Host de origem
192.168.1.50
       ↓
Rede de origem
192.168.1.0/24
       ↓
Roteamento
       ↓
Rede de destino
10.20.30.0/24
       ↓
Host de destino
10.20.30.40
       ↓
TCP/443
       ↓
HTTPS
```

A partir daí, o analista pode investigar se essa comunicação é esperada.

---

## 12. Anomalias relacionadas ao roteamento

O roteamento, por si só, não indica uma atividade maliciosa.

O que pode chamar a atenção do SOC é o **comportamento da comunicação**.

Alguns exemplos:

```text
Host de uma rede acessando
uma rede que normalmente não utiliza
```

```text
Grande quantidade de hosts
acessando uma rede incomum
```

```text
Servidor acessando segmentos
que normalmente não deveria acessar
```

```text
Comunicação inesperada entre
redes administrativas e de usuários
```

```text
Alterações inesperadas nas rotas
ou na infraestrutura de roteamento
```

Esses eventos precisam ser analisados em conjunto com outros dados.

Por exemplo:

```text
Nova comunicação entre redes
        +
Porta incomum
        +
Grande quantidade de destinos
        +
Processo desconhecido no host
        ↓
Investigação
```

Não devemos concluir que existe um ataque apenas porque duas redes diferentes estão se comunicando.

---

## 13. Como pensar como SOC N1

Uma sequência útil é:

```text
Evento
  ↓
IP de origem
  ↓
Rede de origem
  ↓
IP de destino
  ↓
Rede de destino
  ↓
Mesma rede?
  │
  ├── SIM
  │    ↓
  │  comunicação local
  │
  └── NÃO
       ↓
    roteamento
       ↓
  outra rede
       ↓
  protocolo
       ↓
  porta
       ↓
  serviço
       ↓
  comportamento
       ↓
  investigar
```

Por exemplo:

```text
src_ip    = 192.168.1.25
dest_ip   = 10.10.20.50
protocol  = TCP
dest_port = 3389
```

O raciocínio inicial:

```text
192.168.1.25
     ↓
Rede 192.168.1.0/24

10.10.20.50
     ↓
Rede 10.10.20.0/24

Redes diferentes
     ↓
Roteamento
     ↓
TCP
     ↓
3389
     ↓
RDP
```

Agora temos contexto suficiente para começar a investigar a comunicação.

---

## Resumo

| Conceito | Função |
|---|---|
| Roteamento | Processo de decidir para onde encaminhar um pacote |
| Gateway | Ponto utilizado pelo host para alcançar outras redes |
| Tabela de roteamento | Contém informações utilizadas para escolher caminhos |
| Próximo salto | Próximo roteador/dispositivo para o qual o pacote será encaminhado |
| Rota padrão | Caminho utilizado quando não existe uma rota mais específica |
| `0.0.0.0/0` | Rota padrão em IPv4 |
| Rota mais específica | Rota com o maior prefixo/CIDR correspondente ao destino |
| Hop | Cada salto entre dispositivos de camada 3 |
| Tracert/Traceroute | Ferramentas que mostram informações sobre o caminho até um destino |

### Regra para lembrar

```text
Host
  ↓
Destino está na mesma rede?
  │
  ├── SIM
  │    ↓
  │  comunicação local
  │
  └── NÃO
       ↓
    Gateway
       ↓
    Roteador
       ↓
Tabela de roteamento
       ↓
Próximo salto
       ↓
Outro roteador
       ↓
...
       ↓
Rede de destino
       ↓
Host destino
```

E a ideia principal:

> **O gateway é o ponto de saída utilizado pelo host para alcançar outras redes. O roteamento é o processo de decidir por onde o pacote deve ser encaminhado. Cada roteador pode tomar sua própria decisão sobre o próximo salto até que o pacote alcance a rede de destino.**

No SOC N1, pense em:

```text
IP de origem
      ↓
Rede de origem
      ↓
IP de destino
      ↓
Rede de destino
      ↓
Mesma rede?
      ↓
Se não → existe roteamento
      ↓
Protocolo
      ↓
Porta
      ↓
Serviço
      ↓
Comportamento
```
