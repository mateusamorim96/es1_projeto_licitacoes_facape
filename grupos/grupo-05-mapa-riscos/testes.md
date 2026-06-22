# Plano de Testes — Módulo: Mapa de Riscos

## Regra testada

`score = probabilidade × impacto`

Níveis: **1–4 Baixo · 5–9 Médio · 10–14 Alto · 15–25 Crítico**

---

## Casos de Teste — Cálculo de Score e Nível

| ID | Probabilidade | Impacto | Score esperado | Nível esperado |
|----|:---:|:---:|:---:|---|
| T-01 | 1 | 1 | 1 | Baixo |
| T-02 | 2 | 2 | 4 | Baixo — limite superior |
| T-03 | 1 | 5 | 5 | Médio — limite inferior |
| T-04 | 3 | 3 | 9 | Médio — limite superior |
| T-05 | 2 | 5 | 10 | Alto — limite inferior |
| T-06 | 3 | 4 | 12 | Alto |
| T-07 | 3 | 5 | 15 | Crítico — limite inferior, aciona validação jurídica (RN-MR-03) |
| T-08 | 5 | 5 | 25 | Crítico — score máximo possível |

---

## Casos de Teste — Entradas Inválidas

| ID | Entrada | Resultado esperado |
|----|---------|---|
| T-09 | Probabilidade = 0 | ❌ Rejeitado — mínimo é 1 |
| T-10 | Impacto = 6 | ❌ Rejeitado — máximo é 5 |
| T-11 | Probabilidade vazia | ❌ Rejeitado — campo obrigatório |

---

## Casos de Teste — Regras de Negócio

| ID | Cenário | Resultado esperado |
|----|---------|---|
| T-12 | Risco sem mitigação ao gerar mapa | ❌ Sistema bloqueia — exibe lista de pendências (RN-MR-04) |
| T-13 | Score ≥ 15 ao submeter para aprovação | ✅ Sistema exige validação do Jurídico antes da aprovação (RN-MR-03) |
| T-14 | Tentar editar mapa com status APROVADO | ❌ Sistema bloqueia — mapa imutável (RN-MR-07) |

---

## Resumo

| Categoria | Casos |
|-----------|:---:|
| Cálculo de score e nível | 8 |
| Entradas inválidas | 3 |
| Regras de negócio | 3 |
| **Total** | **14** |
