# Máscaras de rede e CIDR

Esses dois conceitos ajudam a entender o IPv4, principalmente para saber qual parte do IP representa a rede e qual represente o host.

## 1. Máscara de rede

A máscara informa quais bits de um endereço IPv4 pertencem à rede e quais pertencem ao host.

Por exemplo:

```
IP: 192.168.1.10
Máscara: 255.255.255.0
```

Podemos visualizar assim:

<table>
  <tr>
    <td>192.168.1</td>
    <td>10</td>
  </tr>
  <tr>
    <td>Rede</td>
    <td>Host</td>
  </tr>
</table>

Nesse caso, a rede é **192.168.1.0** e o dispositivo possui o endereço: **192.168.1.10**

## O que é CIDR

CIDR é uma forma mais curta de representar a máscara.

Portanto, em vez de escrever **255.255.255.0**, podemos escrever **/24**, ou seja, **192.168.1.10/24**, significando:

```text
IP: 192.168.1.10
Máscara: 255.255.255.0
CIDR: /24
```

## 3. O que significa /24

Lembrando que, no IPv4, temos **32 bits**. O /24 indica que os primeiros 24 bits são utilizados para identificar a rede. Então:

<table>
  <tr>
    <td>11111111.11111111.11111111.</td>
    <td>00000000</td>
  </tr>
  <tr>
    <td>REDE</td>
    <td>HOST</td>
  </tr>
</table>
