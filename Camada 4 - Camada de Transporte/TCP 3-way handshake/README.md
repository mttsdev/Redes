# UDP — User Datagram Protocol

## 1. O que é UDP?

O **UDP (User Datagram Protocol)** é um protocolo da **Camada 4 (Transporte)** que permite o envio de datagramas sem estabelecer previamente uma conexão.

Diferentemente do TCP, o UDP **não utiliza o Three-Way Handshake**.

No TCP, antes da troca de dados, existe um processo de estabelecimento de conexão:

```text
Cliente                  Servidor
   │                        │
   │────── SYN ────────────►│
   │◄──── SYN/ACK ──────────│
   │────── ACK ─────────────►│
   │                        │
   │════ Dados ════════════►│
```

No UDP, os dados podem ser enviados diretamente:

```text
Cliente                  Servidor
   │                        │
   │──── Datagrama UDP ────►│
   │                        │
   │◄─── Datagrama UDP ─────│
```

Isso torna o UDP um protocolo **sem conexão (connectionless)** e com menos mecanismos de controle do que o TCP.

> Importante: "sem conexão" não significa que não exista comunicação entre os hosts. Significa que o UDP não estabelece uma conexão de transporte como o TCP.

---

## 2. TCP × UDP

| Característica | TCP | UDP |
|---|---|---|
| Modelo | Orientado à conexão | Sem conexão |
| Three-Way Handshake | Sim | Não |
| Controle de entrega | Maior | Menor |
| Ordenação dos dados | Garantida pelo protocolo | Não garantida |
| Retransmissão | Possui | Não possui |
| Controle de fluxo | Sim | Não |
| Controle de congestionamento | Sim | Não |
| Flags SYN/ACK/FIN/RST | Sim | Não |
| Overhead | Geralmente maior | Geralmente menor |

### Em termos práticos

O TCP possui vários mecanismos para controlar a comunicação. Por isso, ele é adequado quando a **confiabilidade e a ordem dos dados** são importantes.

O UDP possui uma estrutura mais simples. Isso reduz o overhead do protocolo e pode ser vantajoso quando **baixa latência e simplicidade** são mais importantes.

Isso não significa que o UDP seja "pior" que o TCP. São protocolos utilizados para finalidades diferentes.

> Outro detalhe importante: o UDP não garante entrega, ordenação ou retransmissão, mas uma aplicação pode implementar mecanismos próprios de confiabilidade sobre UDP.

---

## 3. Portas UDP

Assim como o TCP, o UDP utiliza **portas** para identificar os serviços e processos envolvidos na comunicação.

Exemplo:

```text
IP de origem:       10.0.0.15
Porta de origem:    53000

IP de destino:      10.0.0.20
Porta de destino:   53

Protocolo:          UDP
```

Nesse caso:

```text
10.0.0.15:53000
       │
       │  UDP
       ▼
10.0.0.20:53
```

Podemos interpretar:

> O host `10.0.0.15` enviou um datagrama UDP originado da porta `53000` para a porta `53` do host `10.0.0.20`.

A porta `53` é **normalmente associada ao DNS**.

### Atenção

Embora a porta 53 seja muito associada ao UDP, **DNS também pode utilizar TCP** em determinadas situações.

Por isso, no SOC, não devemos concluir:

```text
53 = sempre UDP
```

O correto é observar:

```text
Porta 53 + protocolo UDP → DNS sobre UDP
Porta 53 + protocolo TCP → DNS sobre TCP
```

---

## 4. Principais portas UDP

Algumas portas UDP aparecem com frequência em ambientes corporativos e são importantes para um analista SOC N1 conhecer:

| Porta | Serviço | Observação |
|---:|---|---|
| **53** | DNS | Resolução de nomes |
| **67** | DHCP Server | Servidor DHCP |
| **68** | DHCP Client | Cliente DHCP |
| **69** | TFTP | Transferência simples de arquivos |
| **123** | NTP | Sincronização de horário |
| **161** | SNMP | Monitoramento e gerenciamento |
| **162** | SNMP Trap | Recebimento de traps |
| **500** | IKE / IPsec | Negociação de VPN/IPsec |
| **514** | Syslog | Tradicionalmente usado para envio de logs via UDP |

### Observação importante sobre DHCP

O DHCP utiliza principalmente:

```text
UDP 67 → Servidor
UDP 68 → Cliente
```

Por exemplo:

```text
Cliente UDP/68  ─────►  Servidor UDP/67
```

---

## 5. UDP no SOC N1

O UDP aparece frequentemente em investigações porque vários serviços de infraestrutura utilizam esse protocolo.

Um exemplo comum é o DNS:

```text
10.0.0.15 ───► 10.0.0.20:53
```

Isso, isoladamente, pode ser completamente normal.

Porém, durante uma investigação, podemos encontrar um comportamento como:

```text
10.0.0.15 ───► 10.0.0.20:53
10.0.0.15 ───► 10.0.0.21:53
10.0.0.15 ───► 10.0.0.22:53
10.0.0.15 ───► 10.0.0.23:53
10.0.0.15 ───► 10.0.0.24:53
...
```

Nesse cenário, o analista pode investigar:

- quantidade de requisições;
- frequência;
- quantidade de destinos;
- quais domínios estão sendo consultados;
- se o comportamento é esperado para aquele host;
- se existe um padrão de automação ou atividade suspeita.

### Exemplo de comportamento potencialmente suspeito

Imagine:

```text
10.0.0.15
   │
   ├──► DNS 1
   ├──► DNS 2
   ├──► DNS 3
   ├──► DNS 4
   ├──► DNS 5
   ├──► DNS 6
   └──► ...
```

Um grande volume de tráfego UDP **não significa automaticamente um ataque**.

O SOC N1 precisa analisar o **contexto** para determinar se o comportamento é legítimo, anômalo ou potencialmente malicioso.

---

## 6. O que observar em uma investigação UDP?

Para uma análise inicial, alguns campos são especialmente úteis:

```text
IP origem
Porta origem
IP destino
Porta destino
Protocolo
Quantidade de eventos
Frequência
Horário
Host envolvido
Aplicação/serviço relacionado
```

Por exemplo:

```text
Origem:       10.0.0.15
Porta:        53000
Destino:      vários IPs
Porta:        53
Protocolo:    UDP
Frequência:   2.500 eventos em 1 minuto
```

A partir disso, o analista pode começar a perguntar:

> Esse volume é normal para esse host?

> Esses destinos são legítimos?

> Quais domínios estão sendo consultados?

> O comportamento já aconteceu anteriormente?

> Existe algum processo no endpoint responsável por esse tráfego?

Essas perguntas ajudam a transformar um simples evento de rede em uma **triagem de segurança**.

---

## 7. Resumo para SOC N1

```text
UDP
 │
 ├── Camada 4
 ├── Sem conexão
 ├── Não utiliza Three-Way Handshake
 ├── Não garante entrega
 ├── Não garante ordenação
 ├── Não possui retransmissão própria
 └── Utiliza portas
```

Exemplos importantes:

```text
53   → DNS
67   → DHCP Server
68   → DHCP Client
69   → TFTP
123  → NTP
161  → SNMP
162  → SNMP Trap
500  → IKE/IPsec
514  → Syslog
```

Para o SOC N1, o ponto principal não é apenas saber que **"UDP é rápido"**, mas conseguir interpretar **quem está se comunicando, por qual porta, com qual destino, em qual volume e em qual contexto**.