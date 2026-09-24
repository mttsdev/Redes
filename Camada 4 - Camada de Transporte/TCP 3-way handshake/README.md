# TCP 3-way handshake

## 1. O que é TCP 3-way handshake?

O 3-way handshake é o processo usado pelo TCP para estabelecer uma conexão entre o cliente e servidor antes da transmissão normal de dados.

Ele segue a seguinte sequência:
1. SYN
2. SYN-ACK
3. ACK

## 2. SYN

O cliente quer iniciar uma conexão.

```text
10.0.0.15:50000 → 10.0.0.20:22
TCP SYN
```
Podemos interpretar:
> O cliente 10.0.0.15 está tentando iniciar uma conexão TCP com a porta 22 do servidor 10.0.0.20.

O ```SYN``` é a primeira etapa.

## 3. SYN-ACK

O servidor recebe o SYN e responde:

```text
10.0.0.20:22 → 10.0.0.15:50000
TCP SYN-ACK
```

Podendo ser interpretado:

>"Recebi sua solicitação e estou disposto a estabelecer a conexão."

 SYN-ACK combina duas flags:

 ```text
SYN → também estou sincronizando/iniciando minha parte
ACK → reconheci o seu SYN
```

## 4. ACK

O cliente responde:

```text
10.0.0.15:50000 → 10.0.0.20:22
TCP ACK
```

Depois disso, a conexão foi estabelecida e começa a comunicação normal entre as aplicações.

## 5. 3 Way Handshake no SOC N1

Se no SIEM aparece:

```text
10.0.0.15 → 10.0.0.20:22  SYN
10.0.0.20 → 10.0.0.15:50000 SYN-ACK
10.0.0.15 → 10.0.0.20:22  ACK
```

Significa que o Three Way Handshake foi concluído.

agora:

```
10.0.0.15 → 10.0.0.20:22  SYN
```

Se tiver apenas isso, significa que houve uma tentativa de comunicação, mas não sabemos se foi estabelecida.

E diversos SYN:

```text
SYN
SYN
SYN
SYN
SYN
```

Sem SYN-ACK, pode ser por vários motivos: firewall, serviço indisponível, problema de rede ou comportamento suspeito. O SOC precisa investigar o contexto antes de classificar.

