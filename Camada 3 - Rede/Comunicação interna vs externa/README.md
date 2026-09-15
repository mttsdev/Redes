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

É quando um dispositivo da rede interna se comunicando com algo fora da infraestrutura
