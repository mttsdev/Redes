# TCP 3-Way Handshake

## 1. O que é o TCP 3-Way Handshake?

O **3-Way Handshake** é o processo utilizado pelo **TCP** para estabelecer uma conexão entre dois hosts antes da comunicação normal de dados.

Ele acontece em três etapas:

```text
1. SYN
2. SYN-ACK
3. ACK
```

Podemos visualizar assim:

```text
Cliente                                  Servidor
10.0.0.15                                10.0.0.20
     │                                        │
     │────────────── SYN ────────────────────►│
     │                                        │
     │◄──────────── SYN-ACK ──────────────────│
     │                                        │
     │────────────── ACK ────────────────────►│
     │                                        │
     │══════════ Comunicação TCP ═══════════►│
```

Depois do terceiro passo, os dois lados consideram a conexão TCP estabelecida e a comunicação normal pode prosseguir.

---

## 2. SYN

O cliente envia um segmento com a flag **SYN** para iniciar o estabelecimento da conexão.

```text
10.0.0.15:50000 → 10.0.0.20:22
TCP SYN
```

Podemos interpretar:

> O host `10.0.0.15`, utilizando a porta de origem `50000`, está tentando iniciar uma conexão TCP com a porta `22` do host `10.0.0.20`.

A flag:

```text
SYN → solicita o início/sincronização da conexão TCP
```

### No SOC N1

Ao encontrar:

```text
10.0.0.15 → 10.0.0.20:22  SYN
```

a primeira conclusão possível é:

> Houve uma tentativa de iniciar uma conexão TCP com a porta 22.

Neste momento, **a conexão ainda não foi confirmada como estabelecida**.

---

## 3. SYN-ACK

Se o servidor estiver disponível e aceitar a tentativa de conexão, ele responde com um segmento contendo as flags **SYN** e **ACK**.

```text
10.0.0.20:22 → 10.0.0.15:50000
TCP SYN-ACK
```

Podemos interpretar de forma simplificada:

> Recebi sua solicitação e estou respondendo à tentativa de estabelecer a conexão.

O `SYN-ACK` combina duas funções:

```text
SYN → o servidor também está sincronizando sua conexão

ACK → o servidor reconhece o SYN recebido
```

Essa é uma etapa importante porque mostra uma **resposta do destino à tentativa de conexão**.

---

## 4. ACK

Depois de receber o `SYN-ACK`, o cliente responde com **ACK**.

```text
10.0.0.15:50000 → 10.0.0.20:22
TCP ACK
```

De forma simplificada:

> Confirmado.

Agora temos:

```text
SYN
  ↓
SYN-ACK
  ↓
ACK
  ↓
CONEXÃO ESTABELECIDA
```

A partir daí, a comunicação TCP normal pode ocorrer.

---

## 5. Identificando o Handshake no SIEM

Imagine que o SIEM apresente:

```text
10.0.0.15:50000 → 10.0.0.20:22  SYN
10.0.0.20:22   → 10.0.0.15:50000 SYN-ACK
10.0.0.15:50000 → 10.0.0.20:22  ACK
```

Nesse caso, temos a sequência completa:

```text
SYN
 ↓
SYN-ACK
 ↓
ACK
```

Portanto:

> Há evidência de que o Three-Way Handshake foi concluído.

---

## 6. E se aparecer apenas SYN?

Agora imagine:

```text
10.0.0.15:50000 → 10.0.0.20:22
TCP SYN
```

A interpretação correta é:

> O host `10.0.0.15` tentou iniciar uma conexão TCP com a porta `22` do host `10.0.0.20`.

Porém, **somente esse evento não permite afirmar que a conexão foi estabelecida**.

Precisamos procurar a resposta:

```text
SYN → SYN-ACK → ACK
```

Se não encontrarmos o restante da sequência, algumas possibilidades incluem:

```text
Firewall filtrando o tráfego
Serviço indisponível
Host inacessível
Porta filtrada
Problema de rede
Resposta não registrada pelo sensor
Comportamento suspeito
```

Portanto:

```text
SYN sozinho
    ≠
conexão estabelecida
```

E também:

```text
SYN sozinho
    ≠
ataque
```

É apenas uma **tentativa observada**.

---

## 7. E se aparecerem vários SYN?

Imagine:

```text
10.0.0.15 → 10.0.0.20:22  SYN
10.0.0.15 → 10.0.0.20:22  SYN
10.0.0.15 → 10.0.0.20:22  SYN
10.0.0.15 → 10.0.0.20:22  SYN
10.0.0.15 → 10.0.0.20:22  SYN
```

À primeira vista, pode parecer que existem cinco tentativas diferentes.

Mas precisamos ter cuidado.

Esses SYN podem ser **retransmissões da mesma tentativa de conexão**. Por exemplo, o cliente pode enviar um SYN e não receber a resposta esperada, fazendo uma nova transmissão.

Portanto:

```text
5 SYN
    ≠
necessariamente 5 tentativas independentes
```

Para interpretar corretamente, precisamos observar principalmente:

```text
IP origem
IP destino
Porta de destino
Horário
Intervalo entre os SYN
SYN-ACK
ACK
RST
Quantidade de conexões
```

---

## 8. TCP 3-Way Handshake no SOC N1

O Three-Way Handshake é especialmente útil durante uma investigação porque permite determinar **em que ponto uma comunicação TCP está**.

### Cenário A — Apenas SYN

```text
10.0.0.15 → 10.0.0.20:22  SYN
```

Interpretação:

> Houve uma tentativa de iniciar uma conexão TCP.

Ainda não temos evidência suficiente para afirmar que a conexão foi estabelecida.

---

### Cenário B — Handshake completo

```text
10.0.0.15 → 10.0.0.20:22  SYN
10.0.0.20 → 10.0.0.15:50000 SYN-ACK
10.0.0.15 → 10.0.0.20:22  ACK
```

Interpretação:

> O Three-Way Handshake foi concluído.

---

### Cenário C — Muitos SYN sem SYN-ACK

```text
SYN
SYN
SYN
SYN
SYN
```

Interpretação inicial:

> Existem múltiplos SYN observados sem uma resposta SYN-ACK correspondente.

Isso pode ocorrer por motivos legítimos ou suspeitos.

O SOC N1 precisa investigar o contexto antes de classificar o evento.

---

## 9. O que o SOC N1 deve perguntar?

Ao analisar uma tentativa TCP, algumas perguntas são particularmente úteis:

```text
Quem iniciou a conexão?

Qual é o destino?

Qual é a porta?

O destino respondeu com SYN-ACK?

O cliente respondeu com ACK?

O handshake foi concluído?

Houve RST?

Quantos SYN foram enviados?

Os SYN podem ser retransmissões?

O comportamento é esperado para esse host?
```

Essa análise transforma o simples evento:

```text
SYN
```

em uma investigação mais completa sobre o comportamento da comunicação.

---

## 10. Resumo

```text
TCP 3-WAY HANDSHAKE
        │
        ├── 1. SYN
        │      ↓
        │   "Quero iniciar"
        │
        ├── 2. SYN-ACK
        │      ↓
        │   "Recebi e respondi"
        │
        └── 3. ACK
               ↓
           "Confirmado"
               ↓
       CONEXÃO ESTABELECIDA
```

### Para memorizar

```text
SYN       → inicia
SYN-ACK   → responde
ACK       → confirma
```

No contexto de SOC N1:

```text
SYN sozinho
→ tentativa observada

SYN + SYN-ACK
→ resposta à tentativa

SYN + SYN-ACK + ACK
→ handshake concluído
```

E uma regra importante:

```text
Muitos SYN
    ≠
automaticamente ataque
```

É necessário analisar o padrão, o contexto, as respostas e o comportamento do host.