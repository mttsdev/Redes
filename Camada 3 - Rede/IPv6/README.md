# IPv6

O **IPv6 (Internet Protocol version 6)** que veio para resolver um problema de limitação do IPv4 pois, como o mesmo tinha 32 bits, ele tinha "apenas" 4,3 bilhões de endereços, sendo insuficiente. Para isso, o IPv6 tem 128 bits, resolvendo o problema dos endereços.

## Diferença escrita e visual

Enquanto o IPv4 pode aparecer assim:

```text
192.168.1.10
```

O IPv6 pode aparecer assim:

```text
2001:db8:85a3:0000:0000:8a2e:0370:7334
```

Diferentemente do IPv4, o IPv6 conta com hexadecimal e utiliza dois pontos e, em vez de 8 bits e 4 octetos, ele tem 16 bits e 8 grupos chamados de **hextetos**.

## Abreviação do IPv6

É muito comum não encontrar o endereço completo, pois ele pode ser abreviado:

Por exemplo, ele sai disso:

```text
2001:0db8:0000:0000:0000:0000:0000:0001
```

Para isso:

```text
2001:db8::1
```

Mas a abreviação segue regras específicas:

* Os zeros à esquerda podem ser removidos (```0db8 -> db8```);
* Sequência de zeros podem ser substituídos por ```::```.

## IPv6 não possui broadcast

Em vez do broadcast, o IPv6 usa usa multicast para se comuniciar com vários dispositivos.

# Endereços IPv6 especiais

Assim como o IPv4, o IPv6 também tem seus endereços especiais:

### **127.0.0.1( IPv6: ::1 )**

Significa loopback, ou seja, a própria máquina.

---

### **fe80::/10**

Significa **link-local**, são utilizados para comunicação dentro do próprio segmento de rede e são muito comuns no IPv6.

---

### **fc00::/7**

Usados para endereços **Unique Local Address (ULA)**, que são usados em redes privadas/locais
