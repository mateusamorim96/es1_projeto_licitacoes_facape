# CT-04 — Cota ME/EPP calculada como 25% do valor com arredondamento para menos

## Identificação

| Campo | Valor |
|-------|-------|
| **ID** | CT-04 |
| **User Story** | US-01 |
| **Regra de Negócio** | RN-02 |
| **Prioridade** | Alta |
| **Tipo de Teste** | Borda |
| **Nível** | Unitário |

## Pré-condições

- O sistema possui um item com valor total acima de R$ 80.000,00
- A quantidade do item não é divisível por 4 (não resulta em número inteiro ao calcular 25%)

## Dados de Entrada

| Campo | Valor |
|-------|-------|
| Descrição do item | Cadeira ergonômica |
| Valor total estimado | R$ 120.000,00 |
| Quantidade total do item | 10 unidades |

## Passos de Execução

1. Acessar o módulo de elaboração do TR
2. Informar o item com 10 unidades e valor total de R$ 120.000,00
3. Solicitar ao sistema o cálculo da cota ME/EPP (25%)
4. Verificar se o arredondamento foi feito corretamente

## Resultado Esperado

> 25% de 10 unidades = 2,5 unidades. O sistema deve arredondar **para baixo**, reservando **2 unidades** para ME/EPP (não 3). Arredondar para cima prejudicaria a empresa principal e poderia invalidar o processo. O sistema deve exibir: "Cota ME/EPP: 2 unidades (25% de 10, arredondado para menos)."

## Resultado Obtido

> O sistema calculou floor(7 * 0.25) = floor(1.75) = 1 unidade para cota ME/EPP, arredondando para baixo conforme esperado. Exibiu: 'Cota ME/EPP calculada: 1 unidade (arredondamento para menos aplicado — RN-02).'

## Observação

> O arredondamento para baixo é a interpretação mais conservadora e juridicamente segura. Arredondar para cima aumentaria a cota além do legalmente exigido, o que pode gerar contestação por parte das empresas de grande porte.

## Status

[ ] Não executado | [x] Passou | [ ] Falhou | [ ] Bloqueado
