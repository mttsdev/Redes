# Portas e serviços

## 1. O que é uma porta?

Na Camada 3, o IP identifica o host.
Na Camada 4, a porta ajuda qual serviço/processo está recebendo ou originando a comunicação.

Por exemplo:
```text
192.168.1.10:22
```
Onde:
* ```192.168.1.10```: Computador/servidor
* ```22```: porta
* ```TCP```: Protocolo de transporte
* ```22```: Normalmente está associada aso SSH

Então, ```192.168.1.50 → 192.168.1.10:22``` pode ser interpretado como: O computador ```192.168.1.50``` está tentando estabelecer uma comunicação com o serviço SSH do ```192.168.1.10```.

## 2. Porta não é exatamente "o serviço"

A porta é um número usado na comunicação. Existe uma associação convencional entre determinadas portas e determinados serviços.

Exemplos:
<table>
    <th>Porta</th>
    <th>Protocolo</th>
    <th>Serviço normalmente associado</th>
    <tr>
        <td>20/21</td>
        <td>TCP</td>
        <td>FTP</td>
    </tr>
    <tr>
        <td>22</td>
        <td>TCP</td>
        <td>SSH</td>
    </tr>
    <tr>
        <td>23</td>
        <td>TCP</td>
        <td>Telnet</td>
    </tr>
    <tr>
        <td>25</td>
        <td>TCP</td>
        <td>SMTP</td>
    </tr>
    <tr>
        <td>53</td>
        <td>TCP/UDP</td>
        <td>DNS</td>
    </tr>
    <tr>
        <td>80</td>
        <td>TCP</td>
        <td>HTTP</td>
    </tr>
    <tr>
        <td>110</td>
        <td>TCP</td>
        <td>POP3</td>
    </tr>
    <tr>
        <td>143</td>
        <td>TCP</td>
        <td>IMAP</td>
    </tr>
    <tr>
        <td>443</td>
        <td>TCP</td>
        <td>HTTPS</td>
    </tr>
    <tr>
        <td>445</td>
        <td>TCP</td>
        <td>SMB</td>
    </tr>
    <tr>
        <td>3389</td>
        <td>TCP</td>
        <td>RDP</td>
    </tr>
</table>

O "normalmente associado" é importante porque um serviço pode ser configurado para utilizar outra porta. Por exemplo, SSH normalmente usa 22, mas um administrador pode configurá-lo para usar 2222. Ou seja, porta 22 ≠ garantia de que é SSH, é apenas um forte indicativo.

## 3. Porta de origem e destino

Imagine:

```text
192.168.1.50:51543 → 192.168.1.10:443
```

```text
IP origem       Porta origem
192.168.1.50    51543

IP destino      Porta destino
192.168.1.10    443
```

A porta 51543 é uma porta de origem temporária, enquanto 443 é a porta do serviço de destino.

Em uma comunicação comum:

```text
Cliente                         Servidor
192.168.1.50                    192.168.1.10
porta 51543                     porta 443
     │                              │
     └──────── TCP ────────────────►
```

O cliente normalmente utiliza uma porta efêmera para iniciar a conexão.

## 4. Portas e serviços em SOC N1

Muitas das vezes um IP sozinho não é o suficiente para justificar a investigação. Imagine dois eventos:

```text
192.168.1.50 → 192.168.1.10:443
```

```text
192.168.1.50 → 192.168.1.10:3389
```

Os destinos são os mesmos, mas o serviço envolvido é **diferente**. Isso muda completamente o contexto da investigação.

Um SOC N1 pode encontrar algo como:

```text
SRC=192.168.1.50
DEST=10.10.10.20
DST_PORT=3389
PROTOCOL=TCP
```

A primeira interpretação seria:

O host 192.168.1.50 está tentando se comunicar com a porta 3389 do host 10.10.10.20, normalmente utilizada por RDP.

A partir daí, investigaríamos o contexto.

Por exemplo:

Esse computador deveria utilizar RDP?
O destino é um servidor?
O usuário normalmente realiza esse tipo de acesso?
Foram feitas muitas tentativas?
A conexão foi permitida?
Houve várias máquinas tentando a mesma porta?
