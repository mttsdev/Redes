# Rede vs Host

Em uma empresa com vários computadores, precisamos distinguir duas coisas:

```text
Rede: Qual é a "área" ou segmento?
Hosts: Qual dispositivo específico está dentro desta rede?
```

Em uma rede ```192.168.1.0/24``` sabemos que:

<table>
   <tr>
      <td>192.168.1</td>
      <td>.0</td>
   </tr>
   <tr>
      <td>Rede</td>
      <td>Host</td>
   </tr>
</table>

## 1. Parte de Rede e Parte de Host

A parte de rede indica **a qual rede o endereço pertence**. Enquanto a parte de host indica o **dispositivo/endereço específico dentro daquela rede**.
O endereço ```192.168.1.0``` é uma rede. Nesta mesma rede, podemos ter dispositivos com estes endereços:

```text
192.168.1.10
192.168.1.20
192.168.1.30
192.168.1.50
```

Então podemos pensar desta forma:

```text
Rede: 192.168.1.0
         |
         ├── Host: 10
         ├── Host: 20
         ├── Host: 30
         ├── Host: 50
```

São hosts diferentes, mas da mesma rede.

## 2. Quem determina quem começa o host e termina a rede?

A **máscara/CIDR**.

Veja essa situação que tem uma rede com /24:

<table>
   <tr>
      <td>192.168.1</td>
      <td>.0</td>
   </tr>
   <tr>
      <td>Rede</td>
      <td>Host</td>
   </tr>
</table>

Agora com /16:

<table>
   <tr>
      <td>192.168</td>
      <td>.1.0</td>
   </tr>
   <tr>
      <td>Rede</td>
      <td>Host</td>
   </tr>
</table>

## 3. Voltando aos bits

Com /24, temos 24 bits para redes e 8 para host, ele fica assim:

<table>
   <tr>
      <td>11111111.11111111.11111111</td>
      <td>.00000000</td>
   </tr>
   <tr>
      <td>------------- REDE -------------</td>
      <td>-- HOST --</td>
   </tr>
</table>

Agora com /26:
<table>
   <tr>
      <td>11111111.11111111.11111111.11</td>
      <td>000000</td>
   </tr>
   <tr>
      <td>-------------- REDE --------------</td>
      <td>- HOST -</td>
   </tr>
</table>

Aumentar o CIDR significa pegar mais bits dos hosts e colocando para a rede, e vice-versa quando o CIDR diminui.

## 4. Rede vs host no SOC

Imagine em uma empresa:

```text
src_ip = 192.168.10.25
dest_ip = 192.168.10.80
```

Sabemos que os dois estão na mesma rede.

Agora:
```text
src_ip = 192.168.10.25
dest_ip = 192.168.20.80
```

Com /24, eles estão em redes diferentes.

Em uma investigação, isso pode ajudar a responder:

"Essa máquina está se comunicando com outro host dentro do mesmo segmento ou está atravessando redes?"
