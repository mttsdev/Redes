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

Isso corresponde a: 
```text
255.255.255.0
```
Portanto: 
```text
192.168.1.10/24
```
Tem:
```text
Rede: 192.168.1
Host: 10
```

## 4. Por que isso importa?

Por que o computador precisa descobrir se determinado destino está na mesma rede. Exemplo:

```text
PC A: 192.168.1.10/24
PC B: 192.168.1.20/24
```
Estão na mesma rede. Agora:

```text
PC A: 192.168.1.10/24
PC B: 192.168.2.10/24
```

Estão em redes distintas.

## 5. Quanto maior o CIDR, menor a rede

Quanto maior o número depois da /, menor é o número de endereços disponíveis aos hosts:

```text
/24 = 32 - 24 = 8 bits aos hosts
/16 = 32 -16 = 16 bits aos hosts
```

## 6. Exemplos comuns de redes

```text
10.0.0.8/8: Rede privada muito grande para hosts.
172.16.0.0/12;
192.168.1.0/24: Faixa comum para casas, normalmente são menores.
```

## 7. Máscaras e CIDR no SOC

Imagine um alerta:

```text
src_ip=10.10.20.15
dest_ip=10.10.20.50
```
E você sabe que a rede é 10.10.20.0/24, logo, é da mesma rede, mas se fosse:
```text
src_ip=10.10.20.15
dest_ip=10.10.30.50
```

Seria outra rede.
