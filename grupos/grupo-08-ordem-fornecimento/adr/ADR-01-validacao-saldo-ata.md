# ADR-01 — Validação de Saldo da Ata SRP no Momento da Emissão da OF

**Data:** 2026-06-02  
**Status:** Aceito  
**Grupo:** G08 — Controle de Ordem de Fornecimento  

---

## Contexto

Ao emitir uma Ordem de Fornecimento (OF), o sistema precisa garantir que a Ata SRP referenciada possui saldo disponível suficiente. O saldo da ata pode ser consumido por múltiplas OFs ao longo de sua vigência de 12 meses.

A dúvida arquitetural é: **quando e como validar o saldo?**

---

## Decisão

A validação do saldo da ata será feita no momento exato da confirmação da emissão da OF, com bloqueio imediato e dedução atômica do saldo.

O saldo disponível é calculado como:

```
saldo_disponivel = quantidade_registrada_na_ata - soma(quantidade_ofs_emitidas_vinculadas)
```

A OF só é efetivada se `saldo_disponivel >= quantidade_solicitada`.

---

## Alternativas Consideradas

| Opção | Descrição | Motivo da Rejeição |
|-------|-----------|-------------------|
| A — Validar apenas ao salvar rascunho | Verificar saldo ao criar o rascunho da OF | Saldo pode mudar entre o rascunho e a confirmação; outra OF pode ser emitida no intervalo |
| B — Validar somente via relatório periódico | Checar saldo por relatório diário | Inaceitável: permite emissão de OF sem saldo, gerando inconsistência legal |
| **C — Validar no momento da confirmação (escolhida)** | Bloquear e deduzir no momento do commit da OF | Garante consistência; simples de implementar no escopo acadêmico |

---

## Consequências

**Positivas:**
- Saldo sempre consistente com as OFs emitidas.
- Simples de implementar — sem necessidade de mecanismo de reserva ou lock distribuído.
- Mensagem de erro clara para o usuário no momento em que tenta emitir.

**Negativas/Limitações:**
- Em cenário com alta concorrência (múltiplos usuários emitindo OFs simultaneamente), pode haver condição de corrida. Fora do escopo acadêmico deste projeto, mas deve ser tratado com controle transacional em produção.

---

## Referência Normativa

- RN-04: Contratos SRP — emissão de OF respeitando saldo disponível e prazo de validade da ata.
- Lei Federal nº 14.133/2021, Art. 82 — Ata de Registro de Preços.
