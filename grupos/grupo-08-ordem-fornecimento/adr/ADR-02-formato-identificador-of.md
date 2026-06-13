# ADR-02 — Formato do Identificador da Ordem de Fornecimento

**Data:** 2026-06-02  
**Status:** Aceito  
**Grupo:** G08 — Controle de Ordem de Fornecimento  

---

## Contexto

Cada Ordem de Fornecimento precisa de um identificador único e legível por humanos para ser referenciado em documentos físicos, e-mails e registros de auditoria. O identificador precisa ser estável (não muda após criação), único no sistema e facilmente comunicável verbalmente.

---

## Decisão

O identificador da OF seguirá o formato:

```
OF-{ANO}-{SEQUENCIAL_4_DIGITOS}
```

Exemplos: `OF-2026-0001`, `OF-2026-0042`, `OF-2027-0001`

- **ANO:** ano de emissão da OF (4 dígitos).
- **SEQUENCIAL:** número sequencial reiniciado a cada ano, com zero-padding para 4 dígitos.

---

## Alternativas Consideradas

| Opção | Exemplo | Motivo da Rejeição |
|-------|---------|-------------------|
| A — UUID | `3f2e1a4b-...` | Não legível por humanos; difícil de comunicar verbalmente ou citar em documentos físicos |
| B — Apenas sequencial global | `00001` | Sem contexto de ano; confuso em auditorias de exercícios anteriores |
| **C — OF-ANO-SEQ (escolhida)** | `OF-2026-0042` | Legível, autoexplicativo, sem ambiguidade entre exercícios fiscais |
| D — Incluir código do módulo/setor | `OF-G08-2026-0042` | Desnecessariamente longo; G08 é sempre o emissor |

---

## Consequências

**Positivas:**
- Identificador imediatamente interpretável por auditores e servidores.
- Reinício anual facilita relatórios por exercício fiscal.
- Compatível com a forma como OFs físicas são nomeadas na FACAPE hoje.

**Negativas/Limitações:**
- Reinício anual exige controle de sequencial por ano (tabela de contadores). Implementação simples mas necessária.
- Limite de 9.999 OFs por ano (4 dígitos). Suficiente para o porte da FACAPE; pode ser expandido para 6 dígitos se necessário.

---

## Referência

- Contexto do sistema: a FACAPE hoje emite OFs em papel com numeração manual — este formato digitaliza a prática existente, reduzindo resistência à adoção.
