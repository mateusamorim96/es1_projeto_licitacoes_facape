# Contrato de Dados — RQ-INT-01
## G3 Gestão de Atas SRP

---

## Entrada — Recebido do G2 (ETP)

```json
{
  "etpId": "uuid",
  "itemSolicitado": {
    "descricao": "string",
    "categoria": "string",
    "unidadeMedida": "string",
    "quantidadeEstimada": "decimal"
  },
  "dataEmissaoETP": "date"
}
```

### Validações obrigatórias

| Campo | Tipo | Obrigatório | Regra |
|---|---|---|---|
| `etpId` | UUID | Sim | Deve ser único e rastreável ao G2 |
| `itemSolicitado.descricao` | String | Sim | Mín. 10 caracteres |
| `itemSolicitado.categoria` | String | Sim | Usado no UC02 para compatibilidade |
| `itemSolicitado.unidadeMedida` | String | Sim | Usado no UC02 para compatibilidade |
| `itemSolicitado.quantidadeEstimada` | Decimal | Sim | Deve ser > 0 |
| `dataEmissaoETP` | Date | Sim | Deve ser ≤ data atual |

> **Erro de validação:** Se qualquer campo obrigatório estiver ausente, G3 rejeita com erro estruturado e registra log (RQ-INT-05). Veja TI04 no plano de testes.

---

## Saída — Enviado ao G4 (Termo de Referência)

```json
{
  "etpId": "uuid",
  "ataEncontrada": "boolean",
  "ata": {
    "ataId": "uuid",
    "numeroAta": "string",
    "origem": "PROPRIA | TERCEIRO",
    "item": {
      "descricao": "string",
      "valorUnitario": "decimal",
      "saldoDisponivel": "decimal",
      "limiteCarona": "decimal | null"
    },
    "dataValidade": "date"
  },
  "observacoes": "string"
}
```

> Se `ataEncontrada = false`, o campo `ata` é omitido e `observacoes` indica:  
> `"nenhuma ata compatível — seguir fluxo de nova licitação (RN-10)"`

### Campos da saída

| Campo | Tipo | Condição | Descrição |
|---|---|---|---|
| `etpId` | UUID | Sempre | Rastreabilidade (RQ-INT-02) |
| `ataEncontrada` | Boolean | Sempre | Indica se foi encontrada ata compatível |
| `ata` | Object | Apenas se `ataEncontrada = true` | Dados da ata selecionada |
| `ata.origem` | Enum | Sempre que `ata` presente | `PROPRIA` ou `TERCEIRO` |
| `ata.item.limiteCarona` | Decimal ou null | Preenchido apenas se `origem = TERCEIRO` | Calculado por RN-08 |
| `observacoes` | String | Sempre | Notas adicionais ou motivo de não ter encontrado ata |

---

## Evento de Log Associado (RQ-INT-05)

Após enviar o pacote ao G4, G3 registra:

```json
{
  "evento": "RESULTADO_ENVIADO_G4",
  "timestamp": "datetime",
  "etpId": "uuid",
  "ataId": "uuid | null",
  "usuario": "sistema",
  "detalhes": "Pacote enviado com ataEncontrada=true|false"
}
```
