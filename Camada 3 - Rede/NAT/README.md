# NAT (Network Address Translation)

O **NAT** é o mecanismo que permite que endereços IP sejam **traduzidos de um endereço para outro**, principalmente entre redes privadas e a Internet.

Ele é muito importante para entender como um computador com um IP privado consegue se comunicar com um servidor na Internet.

---

## 1. O que o NAT resolve na prática

Imaginemos uma rede interna:

```text
PC A
192.168.1.10
      |
      v
Gateway/NAT
192.168.1.1
      |
      v
internet
```

O computador possui um IP privado: **```192.168.1.10```**. Este endereço não é utilizado como endereço público na Internet.
Então, quando o computador acessa um servidor externo:
```text
PC
192.168.1.10
    |
    v
   NAT
    |
    v
Internet
```

O NAT pode substituir o IP de origem privado por um público.

De forma mais simples:

```text
Antes do NAT:
192.168.1.10 → 8.8.8.8

Depois do NAT:
200.10.20.30 → 8.8.8.8
```

Onde:
* ```192.168.1.10```: IP Privado
* ```200.10.20.30```: IP público utilizado na Internet

Assim, para o servidor externo, a comunicação aparenta vir de ```200.10.20.30```, e não diretamente de ```192.168.1.10```.

## 2. Como a resposta volta?

O NAT mantém informações para saber qual conexão pertence a qual máquina interna.

Imagine dois computadores: O ```192.168.1.10``` e o ```192.168.1.20```. Ambos acessam a Internet através do mesmo IP público: ```200.10.20.30```. Mas o NAT mantém o controle das conexões para conseguir diferenciar os fluxos.

De forma simplificada:

```text
192.168.1.10:50001 → 200.10.20.30:40001
192.168.1.20:50002 → 200.10.20.30:40002
```

assim, o NAT sabe para qual máquina interna encaminhá-las.
Isso é muito associado ao PAT (Port Address Translation), também chamado de NAT overload, em que vários dispositivos compartilham um único IP público usando diferentes portas.

## 3. NAT ≠ Gateway

Eles frequentemente aparecem juntos, mas são conceitos diferentes.

```text
PC A
192.168.1.10
      |
      v
Gateway/NAT
192.168.1.1
      |
      v
Internet
```

**Gateway:** É o ponto de saída da rede local
**NAT**: Traduz os endereços durante a comunicação

Um mesmo equipamento pode exercer as duas funções, como acontece frequentemente em roteadores domésticos.

## 4. NAT no SOC N1

Imaginemos a situação:

```text
src_ip = 200.10.20.30
dest_ip = 185.50.60.70
```

Mas ```200.10.20.30``` pode ser o IP público utilizado por vários computadores internos. Então o SOC pode precisar consultar os registros de NAT para descobrir:

```text
IP público
    ↓
porta
    ↓
horário
    ↓
IP privado
    ↓
máquina interna
    ↓
usuário
```

Por isso, IP público nem sempre identifica exatamente qual máquina interna originou uma conexão.
Além disso, O NAT pode dificultar conexões externas diretamente direcionadas a hosts privados, especialmente em redes domésticas, mas quem efetivamente controla o que pode ou não passar normalmente é o **firewall**.
