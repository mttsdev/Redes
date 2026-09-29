# Conexões e estados

Uma conexão TCP não fica simplesmente em "conectada" e "desconectada". Durante a vida, ela passa por diferentes estados.

## 1. O ciclo de uma conexão TCP

Podemos visualizar assim:

```text
CLOSED
   ↓
SYN-SENT
   ↓
ESTABLISHED
   ↓
FIN-WAIT / CLOSE-WAIT
   ↓
CLOSED
```

Simplificando:

```text
Tentativa
   ↓
Estabelecimento
   ↓
Comunicação
   ↓
Encerramento
   ↓
Fim
```

## 2. CLOSED

Significa que não existe uma conexão TCP estabelecida. Se estiver em SYN ou SYN-ACK, ainda estaria antes de uma conexão se estabelecer.

## 3. SYN-SENT

O cliente enviou um SYN e está esperando a resposta.

```text
Cliente → Servidor
       SYN
```

## 4. SYN-RECEIVED

O servidor recebeu o SYN e está respondendo com SYN-ACK.

```text
Cliente → Servidor
       SYN

Cliente ← Servidor
     SYN-ACK
```

O servidor está aguardando o ACK final.

## 5. 
