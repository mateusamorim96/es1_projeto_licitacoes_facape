# Diagrama de Sequência — G3 Gestão de Atas SRP

## Cenário: Agente pesquisa ata e seleciona uma compatível

```
Agente        Sistema(G3)        BaseDeAtas       Sistema(G2/ETP)      Sistema(G4)
  │                │                   │                  │                  │
  │──recebe ETP────────────────────────────────────────>│                  │
  │                │                   │                  │                  │
  │──UC01: pesquisar(item)──>│         │                  │                  │
  │                │──buscar atas vigentes──>│            │                  │
  │                │<──lista de atas─────────│            │                  │
  │                │──UC02: filtrar compatíveis──         │                  │
  │<──lista filtrada──────────│                           │                  │
  │                │                   │                  │                  │
  │──seleciona ata X────>│              │                  │                  │
  │                │──UC03: calcularSaldo(X)──>│           │                  │
  │                │<──saldo disponível─────────│          │                  │
  │                │                            │          │                  │
  │   [se ata X é de terceiro]                  │          │                  │
  │                │──UC04: calcularLimiteCarona(X)──>│    │                  │
  │                │<──limite OK / violação──────────│     │                  │
  │                │                            │          │                  │
  │<──resultado validação──────│                │          │                  │
  │──confirma ata──────>│                       │          │                  │
  │                │──UC05: registrar vínculo──>│          │                  │
  │                │<──confirmação──────────────│          │                  │
  │                │──UC06: enviar pacote──────────────────────────────────>│
  │                │                            │          │       <──ack────│
  │<──processo concluído──────│                  │          │                 │
```

## Cenário Alternativo: Nenhuma ata encontrada

```
Agente        Sistema(G3)        BaseDeAtas
  │                │                   │
  │──UC01: pesquisar(item)──>│         │
  │                │──buscar atas vigentes──>│
  │                │<──lista vazia──────────│
  │                │──registrar "sem ata"   │
  │<──"Nenhuma ata compatível"─│           │
  │                │──gerar log (RQ-INT-05) │
  │                │──notificar G9 (Auditoria)
```

## Cenário Alternativo: Carona bloqueada (RN-08)

```
Agente        Sistema(G3)        BaseDeAtas
  │                │                   │
  │──seleciona ata de terceiro──>│      │
  │                │──UC04: calcularLimiteCarona──>│
  │                │<──limiteCarona=100, jaAderido=80│
  │──desejado=30───>│                   │
  │                │──valida: 80+30 > 100 → BLOQUEAR
  │<──ALERTA: violação RN-08──────│     │
  │                │──gerar log CARONA_BLOQUEADA
```
