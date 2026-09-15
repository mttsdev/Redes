# Comunicação interna vs. externa

## 1. Comunicação Interna

É quando dois dispositivos se comunicam dentro de uma rede ou dentro da infraestrutura interna da organizaçõa.

Por exemplo: 
```text
PC
192.168.1.10
    |
    v
Servidor
192.168.1.50
```

Os dois utilizam rede privada e estão na mesma rede ```192.168.1.0/24```.

Outro exemplo:
```text
PC
192.168.1.10
   |
   v
Gateway
192.168.1.1
   |
   v
Servidor
10.10.20.50
```

Aqui também temos uma comunicação interna, mesmo em redes diferentes(nem sempre comunicação interna significa necessariamente "mesma rede").

Outro exemplo é uma empresa ter várias vlans, onde a vlan 10 acessa o servidor da vlan 20, se tornando uma comunicação interna, mas o tráfego precisa passar por roteamento.

## 2. Comunicação externa

É quando um dispositivo da rede interna se comunicando com algo fora da infraestrutura interna, normalmente a internet.

Por exemplo, se um PC quer se conectar à Internet, o NAT traduz seu endereço para um endereço IP público.

## 3. privado ≠ interno em todos os casos

Endereços IP como ```192.168.x.x```, ```10.x.x.x``` e ```172.16.x.x - 172.31.x.x``` normalmente são endereços privados utilizados internamente. mas a classificação de "interno e externo" depende do contexto da organização. Por exemplo, uma empresa pode possuir uma infraestrutura interna com várias redes:

```text
10.10.0.0/16
172.16.0.0/16
192.168.100.0/24
```

Todas podem ser internas. Por isso, no SOC, devemos conhecer a infraestrutura ou consultar ferramentas como SIEM, EDR. etc.

## 4. Como aparece em alerta

Imagine o evento:

```text
src_ip = 192.168.10.25
dest_ip = 192.168.20.50
port = 445
```

Onde provavelmente temos uma comunicação interna → interna. Agora:

```text
src_ip = 192.168.10.25
dest_ip = 185.50.60.70
port = 443
```
Provavelmente seria uma comunicação interna → externa
Também temos o caso inverso:

```text
src_ip = 185.50.60.70
dest_ip = 192.168.10.25
port = 443
```

Sendo externa → interna. Esse último caso pode ser interessante para investigação, dependendo do serviço e das regras do firewall.

## 5. Comunicações no SOC

Imagine um computador interno fazendo comunicação via HTTPS para um IP externo, isso pode ser perfeitamente legítimo. Mas agora um IP fazer milhares de conexões a vários IPs externos seria necessário uma investigação aprofundada.

## 6. 
