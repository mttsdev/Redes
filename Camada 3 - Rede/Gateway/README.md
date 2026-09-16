# Gateway

## 1. O que é?

O **gateway** é o dispositivo ou interface de rede que permite que um dispositivo se comunique com **outras redes**.

Quando um computador precisa enviar um pacote para um destino que está fora da sua própria rede, ele normalmente encaminha esse pacote para o **gateway padrão**.

```text
Na mesma rede:

┌──────┐              ┌──────┐
│ PC A │ ───────────► │ PC B │
└──────┘              └──────┘


Em outra rede:

┌──────┐
│ PC A │
└───┬──┘
    │
    ▼
┌─────────┐
│ Gateway │
└────┬────┘
     │
     ▼
┌────────────┐
│ Outra rede │
└─────┬──────┘
      │
      ▼
 ┌────────┐
 │  PC B  │
 └────────┘
```

O gateway geralmente é uma **interface de um roteador** ou de outro dispositivo capaz de realizar roteamento.

---

## 2. Gateway padrão

O **gateway padrão** é o endereço para o qual o dispositivo encaminha o tráfego quando o destino **não pertence à rede local**.

Por exemplo:

```text
IP:              192.168.1.10
Máscara:         255.255.255.0
Gateway padrão:  192.168.1.1
```

Cada informação possui uma função:

```text
192.168.1.10
      │
      └──► Endereço IP do computador


255.255.255.0
      │
      └──► Define qual parte do endereço identifica a rede


192.168.1.1
      │
      └──► Gateway padrão para alcançar outras redes
```

Considerando a rede:

```text
192.168.1.0/24
```

O computador pode verificar se determinado destino está na mesma rede.

### Destino na mesma rede

```text
PC
192.168.1.10
     │
     │ destino: 192.168.1.50
     ▼
192.168.1.50
```

Como o destino pertence à mesma rede, o computador pode tentar realizar a comunicação **diretamente**, sem enviar o pacote ao gateway para roteamento.

### Destino em outra rede

```text
PC
192.168.1.10
     │
     │ destino: 10.10.20.50
     ▼
Gateway
192.168.1.1
     │
     ▼
Outras redes
     │
     ▼
10.10.20.50
```

Nesse caso, o destino está fora da rede `192.168.1.0/24`.

O computador encaminha o pacote para o **gateway padrão**, que então pode realizar o roteamento para a próxima rede.

---

## 3. Gateway não é necessariamente "a Internet"

Um erro comum é pensar:

```text
Gateway = Internet
```

Não exatamente.

O gateway é o **ponto de saída da rede local para outras redes**.

Por exemplo:

```text
PC
192.168.1.10
     │
     ▼
Gateway
192.168.1.1
     │
     ▼
Rede interna
10.10.20.0/24
```

Nesse caso, o gateway está sendo utilizado para acessar **outra rede interna**, não a Internet.

Em outro cenário:

```text
PC
192.168.1.10
     │
     ▼
Gateway
192.168.1.1
     │
     ▼
Internet
```

Aqui o gateway também funciona como ponto de saída para a Internet.

---

## 4. Gateway no contexto de VLANs

O gateway também é importante quando existem várias VLANs.

Por exemplo:

```text
VLAN 10                    VLAN 20

┌──────────┐              ┌─────────────┐
│    PC    │              │   Servidor  │
│10.10.10. │              │10.10.20.50  │
└────┬─────┘              └──────▲──────┘
     │                            │
     ▼                            │
┌──────────────────────────────────────┐
│              Roteador                │
│                                      │
│ Gateway VLAN 10   Gateway VLAN 20    │
└──────────────────────────────────────┘
```

Para um computador da VLAN 10 acessar um servidor da VLAN 20, o tráfego precisa passar por um dispositivo capaz de realizar **roteamento entre as redes**.

---

## 5. Gateway no SOC N1

O gateway pode ajudar o analista a entender **como o tráfego saiu de uma rede e para onde foi encaminhado**.

Por exemplo:

```text
PC
192.168.10.25
     │
     ▼
Gateway
192.168.10.1
     │
     ▼
Internet
     │
     ▼
185.50.60.70
```

Se o SIEM mostrar:

```text
src_ip  = 192.168.10.25
dest_ip = 185.50.60.70
port    = 443
```

o analista pode investigar:

```text
Quem iniciou?
     ↓
192.168.10.25
     ↓
Qual gateway recebeu?
     ↓
192.168.10.1
     ↓
Para qual destino?
     ↓
185.50.60.70
     ↓
Qual porta?
     ↓
443
```

Isso pode ajudar a reconstruir o caminho da comunicação, principalmente quando combinado com logs de **firewall, roteadores, proxy, NAT e SIEM**.

---

## 6. Como pensar como um SOC N1

Ao analisar um evento de rede, algumas perguntas importantes são:

```text
1. Qual é o IP de origem?
           ↓
2. Qual é a rede de origem?
           ↓
3. Qual é o destino?
           ↓
4. O destino está na mesma rede?
           ↓
5. Se não estiver, qual gateway foi utilizado?
           ↓
6. A comunicação era esperada?
           ↓
7. Qual porta/serviço foi utilizado?
           ↓
8. Existem outros eventos relacionados?
```

O gateway, portanto, é uma peça importante para entender o **caminho que o tráfego percorreu entre redes**.

---

## Resumo

| Conceito | Função |
|---|---|
| IP | Identifica o dispositivo/interface na rede |
| Máscara | Permite determinar a rede e identificar se o destino está na rede local |
| Gateway padrão | Próximo ponto utilizado para alcançar destinos fora da rede local |
| Roteador | Realiza o encaminhamento de pacotes entre redes |
| NAT | Pode traduzir endereços privados e públicos |
| SIEM | Pode centralizar e correlacionar eventos relacionados ao tráfego |

### Regra para lembrar

```text
Destino na mesma rede
        ↓
Comunicação direta
        ↓
Gateway normalmente não é usado para rotear esse tráfego


Destino em outra rede
        ↓
Enviar para o gateway padrão
        ↓
Gateway/Roteador
        ↓
Outra rede
```

No SOC N1, o gateway ajuda a responder uma pergunta importante:

> **"Para onde o tráfego foi encaminhado quando precisou sair da rede local?"**
