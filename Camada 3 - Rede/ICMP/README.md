# ICMP

O **ICMP (Internet Control Message Protocol)** pertence à **Camada 3 (Rede)** e é utilizado principalmente para **diagnóstico, controle e comunicação de erros relacionados ao protocolo IP**.

Um dos exemplos mais conhecidos é o comando:

```text
ping
```

---

## 1. ICMP e o ping

Quando executamos:

```text
ping 192.168.1.20
```

o computador envia uma mensagem ICMP para verificar se o destino responde e se existe **conectividade IP** entre os dois pontos.

Uma visualização simplificada:

```text
PC A                         PC B
192.168.1.10                 192.168.1.20
    │                             │
    │ ── ICMP Echo Request ─────► │
    │                             │
    │ ◄── ICMP Echo Reply ────── │
```

O `ping` utiliza principalmente dois tipos de mensagens:

- **Echo Request** → solicitação enviada pelo computador.
- **Echo Reply** → resposta enviada pelo destino.

Se o computador recebe um **Echo Reply**, isso indica que existe conectividade IP e que o destino respondeu à solicitação ICMP.

Porém:

> **Receber um Echo Reply não significa que todos os serviços do computador estão funcionando.**

Por exemplo, um servidor pode responder ao ping, mas estar com seu serviço HTTP indisponível.

```text
ICMP → responde
TCP/443 → não responde
```

Portanto, o ping verifica uma coisa diferente da disponibilidade de uma aplicação específica.

---

## 2. ICMP ≠ TCP/UDP

É importante não confundir ICMP com os protocolos da Camada 4.

Por exemplo:

```text
ping 8.8.8.8
```

utiliza:

```text
ICMP
```

Já uma conexão com uma aplicação pode utilizar:

```text
TCP ou UDP
```

De forma simplificada:

```text
┌──────────────────────────────┐
│          Aplicação           │
├──────────────────────────────┤
│        TCP / UDP             │ ← Camada 4
├──────────────────────────────┤
│            IP                │
│           ICMP               │ ← Camada 3
└──────────────────────────────┘
```

Uma observação importante:

**ICMP não utiliza portas TCP ou UDP.**

Portanto, não faz sentido analisar um evento ICMP procurando algo como:

```text
ICMP → porta 443
```

Portas pertencem principalmente ao TCP e UDP.

---

## 3. ICMP para comunicação de erros

O ICMP não serve apenas para o `ping`.

Ele também pode transportar mensagens relacionadas a problemas na comunicação IP.

Alguns exemplos:

### Destination Unreachable

Indica que determinado destino ou recurso não pôde ser alcançado.

```text
PC ──► Roteador ──► Destino
                 X
                 │
                 ▼
       ICMP Destination
          Unreachable
```

### Time Exceeded

Pode ocorrer quando o pacote excede o limite de saltos permitido.

```text
PC
 │
 ▼
R1
 │
 ▼
R2
 │
 ▼
R3
 │
 ▼
...
 │
 ▼
Limite de saltos atingido
 │
 ▼
ICMP Time Exceeded
```

Essa mensagem também é importante para ferramentas como o `traceroute`/`tracert`.

### Echo Request / Echo Reply

São as mensagens utilizadas pelo `ping`.

```text
Echo Request
     ↓
    ping
     ↓
Echo Reply
```

---

## 4. ICMP no SOC

No SOC, um evento pode aparecer de forma semelhante a:

```text
src_ip  = 10.10.20.50
dest_ip = 10.10.20.60
protocol = ICMP
```

O analista não deve concluir imediatamente que existe um ataque.

Primeiro, precisa entender o contexto.

Perguntas importantes:

```text
Quem é 10.10.20.50?
        ↓
Quem é 10.10.20.60?
        ↓
Esses dispositivos deveriam se comunicar?
        ↓
Qual tipo de ICMP foi utilizado?
        ↓
Foi uma comunicação isolada?
        ↓
Foram dezenas, centenas ou milhares de mensagens?
        ↓
O comportamento é esperado nesse ambiente?
```

---

## 5. ICMP e reconhecimento de rede

O ICMP também pode aparecer em atividades de **reconhecimento**.

Por exemplo, imagine um computador enviando Echo Requests para vários endereços:

```text
10.10.20.50 ──► 10.10.20.1
             ──► 10.10.20.2
             ──► 10.10.20.3
             ──► 10.10.20.4
             ──► 10.10.20.5
             ──► 10.10.20.6
             ──► ...
```

Esse comportamento pode indicar uma tentativa de descobrir **quais hosts estão ativos na rede**.

Isso pode ser parte de uma atividade de **reconhecimento de rede**.

Porém:

> **Vários ICMP Echo Requests não significam automaticamente um ataque.**

Ferramentas de monitoramento, administração e diagnóstico também podem gerar esse tipo de tráfego.

O SOC precisa analisar o contexto.

---

## 6. Um exemplo de investigação

Imagine que o SIEM mostre:

```text
src_ip       = 10.10.20.50
dest_ip      = vários IPs
protocol     = ICMP
icmp_type    = Echo Request
```

E o comportamento seja:

```text
10.10.20.50
     │
     ├──► 10.10.20.1
     ├──► 10.10.20.2
     ├──► 10.10.20.3
     ├──► 10.10.20.4
     ├──► 10.10.20.5
     ├──► 10.10.20.6
     └──► ...
```

Como SOC N1, você poderia pensar:

> "Esse host está realizando várias tentativas de comunicação ICMP contra diferentes endereços. Preciso verificar se isso é esperado ou se pode representar reconhecimento da rede."

Depois, você poderia consultar:

- SIEM
- EDR
- Firewall
- Inventário de ativos
- Logs do host
- Usuário responsável pelo equipamento
- Processo que gerou o tráfego, quando disponível

---

## 7. Como pensar como SOC N1

Para ICMP, algumas perguntas são especialmente úteis:

```text
┌──────────────────────────────────┐
│          Evento ICMP             │
└────────────────┬─────────────────┘
                 │
                 ▼
        Quem iniciou?
                 │
                 ▼
        Qual foi o destino?
                 │
                 ▼
       Qual tipo de ICMP?
                 │
                 ▼
     Um destino ou vários?
                 │
                 ▼
       Qual a frequência?
                 │
                 ▼
        Isso é esperado?
                 │
                 ▼
       Existe outro evento
          relacionado?
                 │
                 ▼
           Investigar
```

---

## Resumo

| Conceito | Função |
|---|---|
| ICMP | Diagnóstico, controle e comunicação de erros relacionados ao IP |
| Echo Request | Solicitação utilizada pelo `ping` |
| Echo Reply | Resposta ao Echo Request |
| Destination Unreachable | Indica que um destino/recurso não pôde ser alcançado |
| Time Exceeded | Indica que o limite de saltos foi excedido |
| ICMP ≠ TCP/UDP | ICMP não utiliza portas TCP/UDP |
| Reconhecimento | Vários ICMP para diferentes hosts podem indicar descoberta de hosts ativos |

### Regra para lembrar

```text
PING
  │
  ▼
ICMP Echo Request
  │
  ▼
Destino
  │
  ▼
ICMP Echo Reply
```

E, no SOC:

```text
ICMP ≠ ataque

Mas...

Muitos ICMP
     +
Vários destinos
     +
Comportamento inesperado
     ↓
Pode justificar investigação
```

O ponto principal é **não analisar apenas o protocolo**, mas sim o comportamento associado a ele.
