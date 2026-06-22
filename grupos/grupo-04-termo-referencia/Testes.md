# Plano de Testes — Módulo: Termo de Referência

## Regra testada

### Regras de Negócio de ME/EPP sob a Lei nº 14.133/2021 (Parametrizadas via Banco de Dados — ADR-02):
* **RN-01 (Cota Exclusiva Integral):** Se o valor total estimado do item for $\le$ R$ 80.000,00, a `CotaExclusiva` (destinada a ME/EPP) recebe 100% da quantidade do lote, e a `CotaAberta` recebe 0%.
* **RN-02 (Divisão de Cotas Padrão):** Se o valor total estimado do item for > R$ 80.000,00, o lote é dividido em: 25% para a `CotaExclusiva` e 75% para a `CotaAberta` (Ampla Concorrência).
* **Função de Arredondamento (ADR-01):** Divisões fracionadas de itens físicos indivisíveis aplicam a função matemática `Ceil` (arredondamento para cima) sempre em favor da `CotaExclusiva`. O saldo restante é alocado para a `CotaAberta` (`CotaAberta = Quantidade Total - CotaExclusiva`).

---

## Casos de Teste — Cálculo e Divisão de Cotas (Regras ME/EPP — Lei nº 14.133/2021)

| ID | Qtd Total | Valor Unitário | Valor Total Estimado | Cota Exclusiva Esperada | Cota Aberta Esperada | Comportamento / Regra Aplicada |
|----|:---:|:---:|:---:|:---:|:---:|---|
| T-01 | 15 | R$ 4.000,00 | R$ 60.000,00 | 15 | 0 |  Cota Exclusiva Integral — Limite inferior (RN-01) |
| T-02 | 20 | R$ 4.000,00 | R$ 80.000,00 | 20 | 0 |  Cota Exclusiva Integral — Limite superior exato (RN-01) |
| T-03 | 100 | R$ 1.200,00 | R$ 120.000,00 | 25 | 75 |  Divisão Exata 25% / 75% — Sem fracionamento (RN-02) |
| T-04 | 10 | R$ 15.000,00 | R$ 150.000,00 | 3 | 7 |  Arredondamento *Ceil* ($2,5 → 3$) para item indivisível (ADR-01) |
| T-05 | 1 | R$ 85.000,00 | R$ 85.000,00 | 1 | 0 |  Arredondamento *Ceil* ($0,25 → 1$) — Lote indivisível de 1 único item |
| T-06 | 5 | R$ 20.000,00 | R$ 100.000,00 | 2 | 3 |  Arredondamento *Ceil* ($1,25 → 2$) — Minimização do saldo da Cota Aberta |

---

## Casos de Teste — Entradas Inválidas e Parâmetros

| ID | Entrada / Cenário | Resultado esperado |
|----|-------------------|---|
| T-07 | Quantidade do item = 0 |  Rejeitado — A quantidade deve ser um número inteiro positivo maior que zero |
| T-08 | Valor Unitário Estimado = R$ 0,00 |  Rejeitado — O valor estimado do item deve ser maior que zero |
| T-09 | Descrição do item vazia |  Rejeitado — O campo de descrição do item é obrigatório para compor o TR |
| T-10 | Banco de dados sem tabela de "Versão da Lei" ativa |  Sistema lança exceção — Bloqueia o cálculo por falta de parâmetros legais (ADR-02) |

---

## Casos de Teste — Regras de Negócio e Integração (Contratos de Fronteira)

| ID | Cenário | Resultado esperado |
|----|---------|---|
| T-11 | Tentar iniciar TR sem ETP associado (G02) |  Sistema bloqueia — O Termo de Referência exige um ETP finalizado na origem (RQ-INT-01) |
| T-12 | Tentar iniciar TR sem Ata SRP associada (G03) |  Sistema bloqueia — Referencial de preços da Ata SRP é obrigatório (RQ-INT-01) |
| T-13 | Finalizar e disparar o preenchimento do TR com sucesso |  Altera estado para `Aguardando Análise de Risco` e dispara payload estruturado para o módulo G05 (RQ-INT-02) |
| T-14 | Execução de qualquer transição de estado no ciclo do TR |  Sistema grava log estruturado com Timestamp, ID do Usuário, ID do TR e versão aplicada da Lei nº 14.133/2021 (Regras de ME/EPP) (RQ-INT-05) |
| T-15 | Tentar alterar valores calculados de cotas em um TR com status `Aguardando Análise de Risco` |  Sistema bloqueia — Após a finalização técnica, as cotas do documento tornam-se imutáveis |

---

## Resumo

| Categoria | Casos |
|-----------|:---:|
| Cálculo e divisão de cotas (Regras ME/EPP — Lei nº 14.133/2021) | 6 |
| Entradas inválidas e parâmetros | 4 |
| Regras de negócio e integração | 5 |
| **Total** | **15** |
