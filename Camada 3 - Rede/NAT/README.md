# NAT (Network Address Translation)

O **NAT (Network Address Translation)** é um mecanismo utilizado para **traduzir endereços IP** durante uma comunicação de rede.

Ele é muito comum em redes privadas que precisam se comunicar com a Internet.

De forma simples:

```text
IP privado
    ↓
   NAT
    ↓
IP público
```

---

## 1. O que o NAT resolve na prática?

Imagine uma rede interna:

```text
PC A
192.168.1.10
      │
      ▼
Gateway/NAT
192.168.1.1
      │
      ▼
Internet
```

O computador possui um endereço IP privado:

```text
192.168.1.10
```

Esse endereço pertence a uma faixa privada e não é utilizado como endereço público na Internet.

Quando o computador acessa um servidor externo, o dispositivo que realiza NAT pode traduzir o endereço de origem.

Antes da tradução:

```text
192.168.1.10 → 8.8.8.8
```

Depois da tradução:

```text
200.10.20.30 → 8.8.8.8
```

Nesse exemplo:

```text
192.168.1.10
     ↓
IP privado da máquina interna

200.10.20.30
     ↓
IP público utilizado na comunicação externa
```

Para o servidor externo, a comunicação aparenta ter vindo de:

```text
200.10.20.30
```

e não diretamente de:

```text
192.168.1.10
```

---

## 2. Como a resposta consegue voltar?

Aqui está uma das partes mais importantes para entender o NAT.

Imagine dois computadores:

```text
PC A → 192.168.1.10
PC B → 192.168.1.20
```

Os dois acessam a Internet utilizando o mesmo IP público:

```text
200.10.20.30
```

O NAT precisa manter informações sobre as conexões para saber **qual comunicação pertence a qual máquina interna**.

Em um cenário utilizando PAT, podemos ter algo semelhante a:

```text
192.168.1.10:50001
        ↓
200.10.20.30:40001
```

Enquanto:

```text
192.168.1.20:50002
        ↓
200.10.20.30:40002
```

Quando chegam as respostas, o NAT consulta sua tabela de tradução e consegue associar cada fluxo ao host interno correto.

Uma representação simplificada:

```text
Internet
   │
   │ resposta
   ▼
┌────────────────────┐
│      NAT/PAT       │
│                    │
│ :40001 → .10:50001 │
│ :40002 → .20:50002 │
└─────────┬──────────┘
          │
          ├──► 192.168.1.10
          │
          └──► 192.168.1.20
```

---

# 3. PAT — Port Address Translation

O mecanismo mais comum em redes domésticas e corporativas é o **PAT (Port Address Translation)**.

Ele permite que vários dispositivos internos compartilhem um único IP público utilizando diferentes portas.

Também é conhecido como:

```text
NAT Overload
```

Por exemplo:

```text
192.168.1.10:50001
        │
        ▼
200.10.20.30:40001
```

E:

```text
192.168.1.20:50002
        │
        ▼
200.10.20.30:40002
```

Observe que:

```text
IP público = igual
Porta pública = diferente
```

Isso permite ao NAT/PAT diferenciar os fluxos.

---

# 4. Tipos de NAT

Existem diferentes formas de NAT.

### NAT estático

Existe uma tradução fixa entre um endereço privado e um endereço público.

Exemplo:

```text
192.168.1.10
      ↕
200.10.20.30
```

A relação é configurada de forma permanente.

Esse tipo pode ser utilizado, por exemplo, quando um serviço interno precisa ser publicado utilizando um endereço público.

---

### NAT dinâmico

Os endereços privados são traduzidos para endereços públicos disponíveis em um determinado conjunto.

Por exemplo:

```text
192.168.1.10
      ↓
200.10.20.30

192.168.1.20
      ↓
200.10.20.31
```

A tradução pode variar conforme a disponibilidade do conjunto de endereços.

---

### PAT

Vários hosts privados utilizam o mesmo endereço público, diferenciando as conexões por portas.

```text
192.168.1.10:50001
        ↓
200.10.20.30:40001

192.168.1.20:50002
        ↓
200.10.20.30:40002
```

Esse é um cenário extremamente comum.

---

# 5. NAT ≠ Gateway

NAT e gateway frequentemente aparecem juntos, mas são conceitos diferentes.

```text
PC
192.168.1.10
      │
      ▼
Gateway
192.168.1.1
      │
      ▼
NAT
      │
      ▼
Internet
```

### Gateway

É o ponto utilizado para encaminhar o tráfego para **outras redes**.

### NAT

É responsável pela **tradução dos endereços** durante a comunicação.

Um mesmo equipamento pode exercer as duas funções.

Isso é muito comum em roteadores domésticos:

```text
Roteador doméstico
       │
       ├── Gateway
       ├── Roteamento
       ├── NAT/PAT
       └── Firewall
```

Mas essas funções são conceitualmente diferentes.

---

# 6. NAT e comunicação externa

Imagine:

```text
PC
192.168.1.10
      │
      ▼
Gateway/NAT
      │
      │ traduz
      ▼
200.10.20.30
      │
      ▼
Internet
      │
      ▼
185.50.60.70
```

O fluxo pode ser representado assim:

```text
192.168.1.10:50001
        ↓
200.10.20.30:40001
        ↓
185.50.60.70:443
```

O servidor externo não precisa conhecer diretamente o endereço privado:

```text
192.168.1.10
```

Ele recebe a comunicação proveniente do endereço público:

```text
200.10.20.30
```

---

# 7. NAT no SOC N1

Essa é uma parte especialmente importante para o SOC.

Imagine que o SIEM mostre:

```text
src_ip  = 200.10.20.30
dest_ip = 185.50.60.70
port    = 443
```

O analista pode inicialmente identificar:

```text
Origem:
200.10.20.30

Destino:
185.50.60.70

Porta:
443
```

Mas existe uma pergunta importante:

> **Qual máquina interna realmente iniciou essa conexão?**

Isso porque:

```text
200.10.20.30
      │
      ├──► 192.168.1.10
      ├──► 192.168.1.20
      ├──► 192.168.1.30
      └──► ...
```

Vários computadores podem estar utilizando o mesmo IP público.

Por isso, o SOC pode precisar consultar os **logs de NAT**.

---

# 8. O que o SOC precisa correlacionar?

Para descobrir qual host interno originou determinada conexão, informações como estas podem ser importantes:

```text
IP público
     ↓
Porta pública
     ↓
Horário
     ↓
Protocolo
     ↓
IP privado
     ↓
Porta privada
     ↓
Host interno
```

Por exemplo:

```text
IP público:    200.10.20.30
Porta pública: 40001
Horário:       14:35:20
Protocolo:     TCP
      ↓
IP privado:    192.168.1.10
Porta privada: 50001
      ↓
Máquina interna
      ↓
Usuário/processo
```

O **horário** é especialmente importante porque as traduções podem mudar ao longo do tempo.

---

# 9. NAT e firewall não são a mesma coisa

Outro ponto importante:

```text
NAT ≠ Firewall
```

O NAT realiza **tradução de endereços**.

O firewall pode aplicar **regras de controle de tráfego**, permitindo ou bloqueando determinadas comunicações.

Por exemplo:

```text
PC
192.168.1.10
      │
      ▼
Firewall
      │
      ▼
NAT
      │
      ▼
Internet
```

Dependendo do equipamento e da arquitetura, essas funções podem estar no mesmo dispositivo.

Mas continuam sendo funções diferentes.

Além disso, o NAT pode dificultar uma conexão externa diretamente direcionada a um host privado quando não existe uma tradução correspondente, mas isso não deve ser confundido com uma política de segurança do firewall.

---

# 10. Como pensar como SOC N1

Ao encontrar um IP público em um alerta, não devemos assumir automaticamente que ele representa uma única máquina.

Podemos seguir uma linha de investigação:

```text
IP público
     ↓
É NAT?
     ↓
Existe porta?
     ↓
Existe horário?
     ↓
Consultar logs de NAT
     ↓
Encontrar IP privado
     ↓
Identificar o host
     ↓
Identificar usuário/processo
     ↓
Analisar o comportamento
```

Por exemplo:

```text
SIEM
src_ip = 200.10.20.30
dest_ip = 185.50.60.70
dest_port = 443
        │
        ▼
Logs de NAT
        │
        ▼
192.168.1.20:50002
        │
        ▼
Host interno
        │
        ▼
Processo responsável
        │
        ▼
Investigação
```

---

# Resumo

| Conceito | Função |
|---|---|
| NAT | Traduz endereços IP |
| NAT estático | Mantém uma tradução fixa |
| NAT dinâmico | Utiliza um conjunto de endereços disponíveis |
| PAT | Permite que vários hosts compartilhem um IP público utilizando portas diferentes |
| Gateway | Encaminha tráfego para outras redes |
| Firewall | Controla o tráfego de acordo com regras |
| Logs de NAT | Permitem relacionar IP/porta públicos com hosts internos |

### Regra para lembrar

```text
IP privado
    ↓
Gateway
    ↓
NAT/PAT
    ↓
IP público
    ↓
Internet
```

E no SOC:

```text
IP público
    ↓
Pode representar vários hosts
    ↓
Consultar NAT
    ↓
IP privado + porta + horário
    ↓
Host interno
    ↓
Usuário/processo
    ↓
Investigar
```

O ponto principal é:

> **O NAT traduz endereços, e o PAT permite que vários dispositivos internos compartilhem um mesmo IP público utilizando portas diferentes. Para o SOC, os registros de NAT são importantes para descobrir qual host interno estava por trás de uma conexão que aparece com um IP público.**
