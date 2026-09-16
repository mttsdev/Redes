# Comunicação interna vs. externa

No contexto de um SOC, entender se uma comunicação é **interna ou externa** é importante para interpretar alertas e identificar comportamentos potencialmente suspeitos.

> **Importante:** comunicação interna não significa necessariamente segura, assim como comunicação externa não significa necessariamente maliciosa.

---

## 1. Comunicação interna

É quando dois dispositivos se comunicam **dentro da rede ou da infraestrutura interna da organização**.

### Exemplo: mesma rede

```text
┌──────────────┐
│      PC      │
│ 192.168.1.10 │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   Servidor   │
│ 192.168.1.50 │
└──────────────┘
```

Nesse caso, os dois dispositivos utilizam endereços privados e estão na mesma rede:

```text
192.168.1.0/24
```

### Exemplo: redes diferentes

Uma comunicação interna também pode acontecer entre **redes diferentes**:

```text
┌──────────────┐
│      PC      │
│ 192.168.1.10 │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   Gateway    │
│ 192.168.1.1  │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   Servidor   │
│ 10.10.20.50  │
└──────────────┘
```

Ainda podemos considerar essa comunicação **interna**, mesmo que os dispositivos estejam em redes diferentes.

Nesse caso, o tráfego precisa passar por **roteamento**.

### Exemplo: comunicação entre VLANs

Uma organização pode possuir várias VLANs:

```text
VLAN 10                  VLAN 20
┌─────────┐              ┌─────────────┐
│   PC    │              │  Servidor   │
│10.10.10.│              │ 10.10.20.50 │
└────┬────┘              └──────▲──────┘
     │                           │
     └──────► Roteador ◄────────┘
```

Se um computador da VLAN 10 acessa um servidor da VLAN 20, temos uma comunicação:

```text
Interna → Interna
```

Porém, o tráfego precisa passar por **roteamento entre as redes/VLANs**.

---

## 2. Comunicação externa

É quando um dispositivo da rede interna se comunica com algo **fora da infraestrutura interna da organização**, normalmente através da Internet.

Por exemplo:

```text
┌──────────────┐
│      PC      │
│ 192.168.1.10 │
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   Gateway    │
│     NAT      │
└──────┬───────┘
       │
       ▼
      🌐
   Internet
```

O **NAT** pode traduzir o endereço IP privado do computador para um endereço IP público durante a comunicação externa.

---

## 3. Privado ≠ interno em todos os casos

Os principais intervalos de IPv4 privados são:

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

Eles são normalmente utilizados em redes internas.

Porém:

> **IP privado não é sinônimo absoluto de "interno".**

A classificação de uma comunicação como interna ou externa depende do **contexto da organização**.

Uma empresa pode possuir várias redes:

```text
10.10.0.0/16
172.16.0.0/16
192.168.100.0/24
```

Todas elas podem fazer parte da infraestrutura interna.

Por isso, durante uma investigação no SOC, é importante conhecer a infraestrutura da organização e utilizar fontes como:

- SIEM
- EDR
- Firewall
- Inventário de ativos
- Documentação de rede

---

## 4. Como isso aparece em um alerta

Imagine o seguinte evento:

```text
src_ip   = 192.168.10.25
dest_ip  = 192.168.20.50
port     = 445
```

Provavelmente temos:

```text
INTERNO → INTERNO
```

Agora:

```text
src_ip   = 192.168.10.25
dest_ip  = 185.50.60.70
port     = 443
```

Provavelmente:

```text
INTERNO → EXTERNO
```

Também podemos encontrar o cenário inverso:

```text
src_ip   = 185.50.60.70
dest_ip  = 192.168.10.25
port     = 443
```

Nesse caso:

```text
EXTERNO → INTERNO
```

Esse tipo de comunicação pode ser relevante para investigação, dependendo do serviço, das regras do firewall e do contexto do ativo.

---

## 5. Comunicação externa não significa maliciosa

Um computador interno pode realizar diversas comunicações legítimas com a Internet:

```text
PC interno
192.168.10.25
      │
      ├────────► Microsoft
      │
      ├────────► Google
      │
      └────────► Outros serviços legítimos
```

Por exemplo, um navegador acessando um site através de:

```text
HTTPS → TCP/443
```

não é, por si só, um indicador de comprometimento.

O SOC precisa analisar **contexto, frequência, destino, processo, usuário e comportamento**.

---

## 6. Comunicação interna também pode ser maliciosa

O fato de a comunicação ser interna não significa que ela seja segura.

Imagine:

```text
┌──────────────────┐
│  PC comprometido │
│ 192.168.10.25    │
└────────┬─────────┘
         │
         │
         ▼
┌──────────────────┐
│ Servidor interno │
│ 192.168.20.50    │
└──────────────────┘
```

Essa comunicação é:

```text
INTERNO → INTERNO
```

Mas, dependendo do contexto, pode representar **movimentação lateral**.

Por exemplo, um computador comprometido tentando acessar vários servidores internos pode ser um comportamento que merece investigação.

---

## 7. Como pensar como um SOC N1

Ao visualizar uma comunicação em um alerta, não devemos concluir imediatamente:

```text
Externo = Malicioso
Interno = Seguro
```

Uma abordagem mais adequada é:

```text
                 Comunicação
                      │
                      ▼
             ┌─────────────────┐
             │ Interna ou       │
             │ externa?         │
             └────────┬────────┘
                      │
          ┌───────────┴───────────┐
          ▼                       ▼
      INTERNA                  EXTERNA
          │                       │
          ▼                       ▼
   Quem comunicou?          Para quem?
   Qual destino?            Qual destino?
   Qual porta?              Qual porta?
   Qual processo?           Qual processo?
   É esperado?              É esperado?
          │                       │
          └───────────┬───────────┘
                      ▼
               Analisar contexto
                      │
                      ▼
              Classificar alerta
```

A pergunta principal não é apenas **"é interno ou externo?"**.

É:

> **"Essa comunicação faz sentido dentro do contexto desse ambiente?"**

---

## Resumo

| Comunicação | Exemplo | Possível interpretação |
|---|---|---|
| Interno → Interno | PC → servidor | Acesso interno legítimo ou possível movimentação lateral |
| Interno → Externo | PC → Internet | Navegação, atualização, serviço externo ou possível C2 |
| Externo → Interno | Internet → servidor | Pode ser legítimo, mas depende do serviço e das regras |
| Privado → Privado | `192.168.x.x → 10.x.x.x` | Pode ser comunicação interna entre redes diferentes |
| Público → Público | Internet → Internet | Comunicação externa entre entidades/serviços |

### Regra para lembrar

```text
IP privado ≠ necessariamente "interno"
IP público ≠ necessariamente "malicioso"

Interno ≠ seguro
Externo ≠ malicioso
```

No SOC, **o contexto da comunicação é tão importante quanto os endereços IP envolvidos**.
