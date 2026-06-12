# Matriz de Rastreabilidade — Grupo 08
## Módulo: Controle de Ordem de Fornecimento (OF)

**Integrantes:** Vitor Barros, Filipe Silva, Michel Batista, João Pedro Carvalho, Kelvin Keite
**Disciplina:** Engenharia de Software I — 2026.1

---

## Legenda

| Símbolo | Significado |
|---------|-------------|
| X | Relação direta (o CT valida esta US ou RN) |
| — | Sem relação |

---

## User Stories do Módulo

> As user stories abaixo correspondem ao subconjunto do backlog diretamente rastreado pelos casos de teste.
> A numeração original completa (US-01 a US-10) está em `backlog.md`.

| ID (Backlog) | Descrição |
|--------------|-----------|
| US-04 | Como chefe de licitações, quero que o sistema aplique automaticamente as regras de cota ME/EPP por item, para garantir conformidade com a Lei Complementar 123/2006 |
| US-05 | Como chefe de compras, quero que o sistema detecte e impeça o fracionamento de despesa, para evitar irregularidades no processo licitatório |
| US-02 | Como chefe de compras, quero emitir Ordens de Fornecimento com verificação automática de saldo e validade da ata SRP, para garantir que as OFs sejam legais e válidas |
| US-06 | Como gestor de contratos, quero que o sistema controle o limite de adesão a atas alheias (carona), para não ultrapassar 50% do quantitativo original |
| US-07 | Como chefe de licitações, quero que o sistema valide e calcule a cotação de preços com mínimo de 3 fornecedores, para garantir a competitividade do processo |

---

## Regras de Negócio

| ID | Descrição Resumida |
|----|-------------------|
| RN-01 | Itens com valor <= R$ 80.000 são exclusivos para ME/EPP |
| RN-02 | Itens com valor > R$ 80.000 devem ter 25% do quantitativo reservado para ME/EPP |
| RN-03 | Fracionamento de despesa é proibido — itens do mesmo tipo consolidados em um único processo por ano |
| RN-04 | Contratos SRP: emissão de OF respeitando saldo disponível e prazo de validade da ata |
| RN-08 | Adesão a ata alheia (carona) limitada a 50% do quantitativo original |
| RN-10 | Cotação com mínimo de 3 fornecedores distintos |

---

## Matriz Casos de Teste x User Stories

| Caso de Teste | US-04 | US-05 | US-02 | US-06 | US-07 |
|--------------|-------|-------|-------|-------|-------|
| CT-01 — Item abaixo de R$ 80k (exclusivo ME/EPP) | X | — | — | — | — |
| CT-02 — Item acima de R$ 80k (cota 25%) | X | — | — | — | — |
| CT-03 — Item exatamente R$ 80k (borda) | X | — | — | — | — |
| CT-04 — Arredondamento da cota 25% | X | — | — | — | — |
| CT-05 — DFD duplicado: alerta e mescla | — | X | — | — | — |
| CT-06 — Dois processos no mesmo exercício | — | X | — | — | — |
| CT-07 — OF com saldo exato: permite | — | — | X | — | — |
| CT-08 — OF com saldo insuficiente: bloqueia | — | — | X | — | — |
| CT-09 — OF em ata vencida: bloqueia | — | — | X | — | — |
| CT-10 — Adesão 50% do quantitativo: permite | — | — | — | X | — |
| CT-11 — Adesão 51% do quantitativo: bloqueia | — | — | — | X | — |
| CT-12 — Cotação com 2 fornecedores: bloqueia | — | — | — | — | X |
| CT-13 — Cotação com 3 fornecedores: calcula média | — | — | — | — | X |

---

## Matriz Casos de Teste x Regras de Negócio

| Caso de Teste | RN-01 | RN-02 | RN-03 | RN-04 | RN-08 | RN-10 |
|--------------|-------|-------|-------|-------|-------|-------|
| CT-01 — Item abaixo de R$ 80k | X | — | — | — | — | — |
| CT-02 — Item acima de R$ 80k | — | X | — | — | — | — |
| CT-03 — Item exatamente R$ 80k (borda) | X | X | — | — | — | — |
| CT-04 — Arredondamento da cota 25% | — | X | — | — | — | — |
| CT-05 — DFD duplicado: alerta e mescla | — | — | X | — | — | — |
| CT-06 — Dois processos no mesmo exercício | — | — | X | — | — | — |
| CT-07 — OF com saldo exato: permite | — | — | — | X | — | — |
| CT-08 — OF com saldo insuficiente: bloqueia | — | — | — | X | — | — |
| CT-09 — OF em ata vencida: bloqueia | — | — | — | X | — | — |
| CT-10 — Adesão 50%: permite | — | — | — | — | X | — |
| CT-11 — Adesão 51%: bloqueia | — | — | — | — | X | — |
| CT-12 — Cotação 2 fornecedores: bloqueia | — | — | — | — | — | X |
| CT-13 — Cotação 3 fornecedores: calcula média | — | — | — | — | — | X |

---

## Resumo de Cobertura

| Regra de Negócio | Casos de Teste que cobrem |
|-----------------|--------------------------|
| RN-01 | CT-01, CT-03 |
| RN-02 | CT-02, CT-03, CT-04 |
| RN-03 | CT-05, CT-06 |
| RN-04 | CT-07, CT-08, CT-09 |
| RN-08 | CT-10, CT-11 |
| RN-10 | CT-12, CT-13 |

| User Story | Casos de Teste que cobrem |
|-----------|--------------------------|
| US-04 | CT-01, CT-02, CT-03, CT-04 |
| US-05 | CT-05, CT-06 |
| US-02 | CT-07, CT-08, CT-09 |
| US-06 | CT-10, CT-11 |
| US-07 | CT-12, CT-13 |
