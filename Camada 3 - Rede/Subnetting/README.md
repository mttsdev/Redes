# Subnetting

**Subnetting** significa dividir uma rede maior em redes menores.

Imagine a seguinte rede:

```text
192.168.1.0/24
```

Em vez de deixar todos os dispositivos na mesma rede, podemos dividi-la em várias sub-redes. Por exemplo:

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

Assim temos 4 sub-redes.

## 1. Por que fazer subnetting?

Imagine que em uma empresa exista diversos visitantes, pessoas de TI e do RH, podemos dividir a rede assim:

```text
RH: 192.168.1.0/26
TI: 192.168.1.64/26
Visitantes: 192.168.1.128/26
```
Isso permite organizar melhor a rede e controlar o tráfego entre os segmentos.
Para SOC isso é fundamental pois, ao analisarmos um alerta, podemos descobrir que o tráfego veio de uma sub-rede específica. Exemplo:

```text
src_ip=192.168.1.70/24
dest_ip=192.168.1.20/24
```

Como no src_ip indica que é 192.168.1.70, significa que é de TI.

## 2. O que acontece com os hosts?

Em uma rede de /24, sabemos que dos 32 bits, 24 são para rede, restando 8 para hosts.

Com 8 bits:
```text
2^8 = 256 endereços possíveis
```
Mas não é por que temos 256 endereços disponíveis que todos serão aplicados aos dispositivo pois, um vai para a rede e outro para broadcast, sendo assim, 254 destinados aos hosts.

Exemplos com outros CIDRs:

```text
/26 = 32 - 26 = 6 bits > 2^6 = 64 - 2 = 62 bits aos hosts
/25 = 32 - 25 = 7 bits > 2^7 = 128 - 2 = 126 bits aos hosts
/23 = 32 - 23 = 9 bits > 2^9 = 512 - 2 = 510 bits aos hosts
/22 = 32 - 22 = 10 bits > 2^10 = 1024 - 2 = 1022 bits aos hosts
```

## 3. Como funciona a distribuição de hosts no prefixo /26

Dividindo a rede em /26, temos:

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

Elas são distribuídas assim:

```text
192.168.1.0/26 até 192.168.1.63/26
192.168.1.64/26 até 192.168.1.127/26
192.168.1.128/26 até 192.168.1.191/26
192.168.1.192/26 até 192.168.1.255/26
```
Todos as redes tem a mesma quantia de endereços. Mas lembrando, .0 até .63 **não são destinados apenas aos hosts!**. Ocorre desta forma:

```text
Rede: 192.168.1.0
Hosts: 192.168.1.1 até 192.168.1.62
Broadcast: 192.168.1.63
```
De forma mais simples, o primeiro ip de uma sub-rede será para a **rede**, enquanto a última será para o broadcast.

