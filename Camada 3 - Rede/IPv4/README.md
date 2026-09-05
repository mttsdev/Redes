# IPv4

O IPv4(Internet Protocol version 4) e uma versão do IP que utiliza 32 bits. <br><br>
Endereço IPv4 com normalmente aparece: 192.168.1.10 <br><br>
Os 32 bits são separados pelos 4 octetos de um IP, cada um recebendo 8 bits(8 bits para 192, 8 bits para 18 bits para 1 e 8 bits para 10).<br>
Com o formato IPv4, os 4 octetos vão até 255(número máximo: 255.255.255.255), IPs escritos assim: 192.168.1.300 são inválidos.<br>

O IPv4 não serve apenas para dar um número ao computador, ele possui **uma parte que representa a rede e outra que representa os hosts.**<br>
Exemplo: 192.168.1.10/24<br><br>

Neste caso, 24 bits são destinadas à **rede**(192.168.1.), e o restante destinadas a **hosts**(10).

## IP trabalha sozinho?

Não, em uma comunicação, além dele, temos diversos protocolos que ajudam a estabelecer uma comunicação como: HTTP/HTTPS, TCP, IP, etc.<br>
O IPv4 é utilizado principalmente no **endereçamento e encaminhamento dos pacotes de redes**.<br><br>

## IPv4 Especiais

Esses IPv4 tem funções específicas:

* **127.0.0.1**: localhost/lookback
* **169.254.x.x**: É uma faixa link-local usada em determinadas situações quando um dispositivo não consegue obter um endereço IP via DHCP.
* **0.0.0.0**: Pode representar "este host", "todas as interfaces" ou uma rota padrão, dependendo do contexto.
