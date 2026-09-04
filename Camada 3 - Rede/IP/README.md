# IP

O **IP(Internet Protocol)** é um endereço lógico utilizado para identificar um dispositivo dentro de uma rede e permite que pacotes sejam encaminhados até o destino. 

Exemplo: <br>PC 1 de IP **192.168.1.10** ----COMUNICAÇÃO-----> PC 2 de IP **192.168.1.20**

Exemplo de evento de rede:<br> 
src_ip=192.168.1.10 = quem enviou um pacote <br>
dest_ip=192.168.1.20 = quem recebeu um pacote <br>

## Diferença entre IP e MAC

O MAC está relacionado à interface de rede e é utilizado na camada 2.<br>
O IP é um endereço lógico, utilizado para identificar redes e dispositivos na camada 3. Ele também pode mudar quando muda rede.

## IP público e IP privado

Um **IP Público** é um endereço utilizado para comunicação em redes públicas, como a Internet.<br>
Exemplos: 8.8.8.8 e 1.1.1.1

Já um **IP privado** é muito comuns em empresas. normalmente esses IPs vem nessas faixas:<br>
10.0.0.0 --> 10.255.255.255<br>
172.16.0.0 --> 172.31.255.255<br>
192.168.0.0 --> 192.168.255.255<br>

Todos os IPs dentro dessas faixas são considerados IPs privados.
