# Trilha de Auditoria e Rastreabilidade
## G3 Gestão de Atas SRP — RQ-INT-05 / RQ-INT-02

---

## Eventos de Log Estruturados (RQ-INT-05)

O módulo G3 registra os seguintes eventos de forma estruturada para consumo pelo G9 (Auditoria/BI):

```json
{
  "evento": "PESQUISA_ATA_REALIZADA | ATA_SELECIONADA | ATA_NAO_ENCONTRADA | CARONA_BLOQUEADA | RESULTADO_ENVIADO_G4",
  "timestamp": "datetime",
  "etpId": "uuid",
  "ataId": "uuid | null",
  "usuario": "string",
  "detalhes": "string"
}
```

### Descrição dos Eventos

| Evento | Quando ocorre | `ataId` | Exemplo de `detalhes` |
|---|---|---|---|
| `PESQUISA_ATA_REALIZADA` | Ao concluir UC01 | null ou id da lista | `"3 atas encontradas para categoria=TI, unidade=UN"` |
| `ATA_SELECIONADA` | Ao concluir UC05 | id da ata selecionada | `"Ata #2024/001 selecionada. Saldo: 70 UN"` |
| `ATA_NAO_ENCONTRADA` | Fluxo alternativo UC01 | null | `"Nenhuma ata compatível. Recomendado: nova licitação (RN-10)"` |
| `CARONA_BLOQUEADA` | Fluxo alternativo UC04 | id da ata tentada | `"Carona bloqueada: solicitado=30, disponível=20 (RN-08)"` |
| `RESULTADO_ENVIADO_G4` | Ao concluir UC06 | id da ata ou null | `"Pacote enviado com ataEncontrada=true"` |

---

## Rastreabilidade Ponta a Ponta (RQ-INT-02)

```
Demanda (G1) → DFD → ETP (G2) → [G3: Pesquisa de Ata] → TR (G4) → Edital (G6) → Acompanhamento (G7) → OF (G8)
```

### Como G3 garante rastreabilidade

1. **Mantém `etpId` em toda `PesquisaAta`** — cada consulta é vinculada ao ETP que a originou
2. **Inclui `etpId` no pacote enviado ao G4** — a rastreabilidade continua no módulo seguinte
3. **Registra todos os eventos com `etpId` associado** — G9 pode reconstituir o histórico completo de qualquer demanda

### Exemplo de trilha rastreável

```
etpId: "abc-123"
  │
  ├── G1: Demanda criada → DFD → ETP gerado (etpId: abc-123)
  │
  ├── G3: PESQUISA_ATA_REALIZADA (etpId: abc-123, timestamp: ...)
  ├── G3: ATA_SELECIONADA (etpId: abc-123, ataId: xyz-456, ...)
  ├── G3: RESULTADO_ENVIADO_G4 (etpId: abc-123, ataId: xyz-456, ...)
  │
  └── G4: TR elaborado com referência à ata xyz-456 e ao ETP abc-123
```

---

## Indicadores para G9 (Auditoria/BI)

Os eventos acima permitem ao G9 calcular:

- **% de demandas atendidas por ata existente** → proporção de `ATA_SELECIONADA` vs `ATA_NAO_ENCONTRADA`
- **Número de caronas bloqueadas** → contagem de `CARONA_BLOQUEADA`
- **Tempo médio de pesquisa** → diferença de timestamp entre `PESQUISA_ATA_REALIZADA` e `ATA_SELECIONADA`
- **Módulos com maior demanda por nova licitação** → análise de `ATA_NAO_ENCONTRADA` por categoria
