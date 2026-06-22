# Modelo de Domínio — G3 Gestão de Atas SRP

## Entidades e Atributos

```
┌──────────────────────────────────────┐
│                 Ata                  │
├──────────────────────────────────────┤
│ + id: UUID                           │
│ + numeroAta: String                  │
│ + orgaoGerenciador: String           │
│ + origem: Enum {PROPRIA, TERCEIRO}   │
│ + dataAssinatura: Date               │
│ + dataValidade: Date                 │
│ + status: Enum {VIGENTE, EXPIRADA,   │
│            CANCELADA}                │
├──────────────────────────────────────┤
│ + estaVigente(): Boolean             │
│ + calcularSaldo(): Decimal           │
└──────────────────────────────────────┘
               │ 1
               │
               │ N
┌──────────────────────────────────────┐
│              ItemAta                 │
├──────────────────────────────────────┤
│ + id: UUID                           │
│ + ataId: UUID (FK)                   │
│ + descricao: String                  │
│ + categoria: String                  │
│ + unidadeMedida: String              │
│ + quantidadeRegistrada: Decimal      │
│ + valorUnitario: Decimal             │
│ + fornecedorId: UUID (FK)            │
├──────────────────────────────────────┤
│ + saldoDisponivel(): Decimal         │
│ + limiteCarona(): Decimal            │
└──────────────────────────────────────┘
               │ 1
               │
               │ N
┌──────────────────────────────────────┐
│           Empenho/OF                 │
├──────────────────────────────────────┤
│ + id: UUID                           │
│ + itemAtaId: UUID (FK)               │
│ + quantidadeEmpenhada: Decimal       │
│ + tipoOrigem: Enum {PROPRIO, CARONA} │
│ + dataEmpenho: Date                  │
│ + processoOrigemId: UUID (FK → ETP)  │
└──────────────────────────────────────┘

┌──────────────────────────────────────┐
│             Fornecedor               │
├──────────────────────────────────────┤
│ + id: UUID                           │
│ + razaoSocial: String                │
│ + cnpj: String                       │
└──────────────────────────────────────┘

┌──────────────────────────────────────┐
│      PesquisaAta (registro UC01)     │
├──────────────────────────────────────┤
│ + id: UUID                           │
│ + etpOrigemId: UUID (FK → G2)        │
│ + criterioBusca: String              │
│ + dataConsulta: DateTime             │
│ + ataSelecionadaId: UUID (FK, null)  │
│ + resultadoEnviadoG4: Boolean        │
└──────────────────────────────────────┘
```

## Relacionamentos

- `Ata` 1 — N `ItemAta` (uma ata pode ter vários itens registrados)
- `ItemAta` 1 — N `Empenho/OF` (um item pode ter vários empenhos parciais ao longo da vigência)
- `ItemAta` N — 1 `Fornecedor`
- `PesquisaAta` N — 1 `Ata` (uma pesquisa resulta na seleção de uma ata)
- `PesquisaAta` 1 — 1 `ETP` (vindo do G2, referência externa)

## Regras de Negócio nas Entidades

| Entidade | Regra | Referência |
|---|---|---|
| `Ata.estaVigente()` | Retorna true se `dataValidade >= hoje` e `status == VIGENTE` | RN-04 |
| `ItemAta.saldoDisponivel()` | `quantidadeRegistrada − SUM(empenhos.quantidadeEmpenhada)` | RN-04 |
| `ItemAta.limiteCarona()` | `quantidadeRegistrada × 0.5` (apenas quando `Ata.origem == TERCEIRO`) | RN-08 |
