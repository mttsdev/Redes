# Roteamento

## 1. O que é o roteamento?

Roteamento vem logo depois que o pacote passa pelo gateway. Ele é o processo de decidir por qual caminho o pacote deve seguir para chegar ao destino.

Exemplo:

```text
PC A
192.168.1.10
   |
   v
Gateway
192.168.1.1
   |
   v
Roteador
   |
   +------> Rede 10.0.0.0/24
   |
   +------> Rede 172.16.0.0/16
   |
   +------> Internet
```

O PC quer acessar o ```10.0.0.50```, mas percebe que não está na mesma rede. Para isso, ele envia o pacote ao Gateway para que chegue à rede, mas depois o roteador precisa saber por onde encaminhar o pacote para que ele chegue ao destino. É aí que entra o **roteamento**.

---

## 2. Tabela de Roteamento

Para decidir aonde enviar o pacote, o roteador tem uma **Tabela de Roteamento**:

```text
Destino              Próximo caminho
192.168.1.0/24       rede local
10.0.0.0/24          interface X
172.16.0.0/16        interface Y
0.0.0.0/0            saída padrão
```

Quando chega um pacote destinado a ```10.0.0.50```, o roteador procura na tabela para ver se existe uma rota para ele.

## 3. E se não houver destino?

Ele é enviado à rota padrão: ```0.0.0.0```. Muito comum para representar a saída para a internet.

Uma observação: **Roteamento não significa que um roteador sabe o caminho inteiro**.

Em uma rede maior, existem vários roteadores, e cada um toma sua decisão sobre qual será o próximo caminho.

## Roteamento para SOC N1

O que importa é conseguir entender uma comunicação como:

```text
src_ip = 192.168.1.50
dest_ip = 10.20.30.40
```

e raciocinar:

```text
192.168.1.50 pertence à rede 192.168.1.0/24.
10.20.30.40 pertence a outra rede.
O host precisa utilizar seu gateway.
O tráfego será encaminhado através de roteamento.
Eventualmente, outros roteadores podem participar do caminho.
```
