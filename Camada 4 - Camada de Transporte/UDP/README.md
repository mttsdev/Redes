# UDP

## 1. O que é UDP?

O UDP(User Datagram Protocol) e ele, diferentemente do TCP, não utiliza o Three Way Handshake. Ou seja:

```text
Cliente ───────────► Servidor
       envia diretamente
```
## 2. TCP x UDP

<table>
  <th>TCP</th>
  <th>UDP</th>
  <tr>
    <td>Orientado à conexão </td>
    <td>Sem conexão</td>
  </tr>
  <tr>
    <td>Possui handshake</td>
    <td>Não possui handshake</td>
  </tr>
  <tr>
    <td>Mais controle de entrega</td>
    <td>Menos controle </td>
  </tr>
  <tr>
    <td>Garante ordenação/retransmissão</td>
    <td>Não garante</td>
  </tr>
  <tr>
    <td>Possui flags como SYN/ACK/FIN/RST</td>
    <td>Não possui essas flags TCP</td>
  </tr>
  <tr>
    <td>Geralmente mais overhead</td>
    <td>Geralmente menor overhead</td>
  </tr>
</table>

Lembrando: Isso não significa que o UDP seja o pior, ele é essencial em momentos que precisamos de mais velocidade e menos overhead.

## 3. Portas UDP

Mesmo sendo UDP, ainda temos portas, como a ```53```, normalmente associada ao **DNS**. Então para SOC:

```text
IP origem:       10.0.0.15
Porta origem:    53000

IP destino:      10.0.0.20
Porta destino:   53

Protocolo:       UDP
```

> O host 10.0.0.15 enviou um datagrama UDP para a porta 53 do host 10.0.0.20, normalmente utilizada pelo DNS.

## 4. Portas UDP principais

Para SOC N1, algumas portas são importantes:

```text
53    → DNS
67/68 → DHCP
69    → TFTP
123   → NTP
161   → SNMP
162   → SNMP traps
500   → IKE/IPsec
514   → Syslog
```
## 5. UDP no SOC N1

o protocolo UDP pode aparecer diversas vezes em investigações porque muitos serviços de infraestrutura usam UDP.
Enquanto pode existir uma conexão com um ip da porta 53, podemos ver uma conexão a vários IPs:53, gerando um tráfego anormalmente grande.
