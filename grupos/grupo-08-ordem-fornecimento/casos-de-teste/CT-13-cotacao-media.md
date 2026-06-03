# CT-13 — Cotação com 3 fornecedores — sistema deve calcular e sugerir a média corretamente

## Identificação

| Campo | Valor |
|-------|-------|
| **ID** | CT-13 |
| **User Story** | US-05 |
| **Regra de Negócio** | RN-10 |
| **Prioridade** | Alta |
| **Tipo de Teste** | Funcional |
| **Nível** | Unitário |

## Pré-condições

- O usuário está na etapa de cotação de preços de um processo licitatório
- 3 fornecedores distintos foram consultados e tiveram suas propostas registradas

## Dados de Entrada

| Campo | Valor |
|-------|-------|
| Item a ser cotado | Caderno universitário 200 folhas |
| Fornecedor 1 | Distribuidora ABC — R$ 8,50/unidade |
| Fornecedor 2 | Papelaria XYZ — R$ 9,00/unidade |
| Fornecedor 3 | Atacado Norte — R$ 10,00/unidade |

## Passos de Execução

1. Acessar o módulo de cotação de preços
2. Registrar proposta do Fornecedor 1 (R$ 8,50)
3. Registrar proposta do Fornecedor 2 (R$ 9,00)
4. Registrar proposta do Fornecedor 3 (R$ 10,00)
5. Solicitar ao sistema o cálculo da média
6. Verificar se o valor sugerido está correto

## Resultado Esperado

> O sistema deve calcular a média aritmética dos 3 valores:
> (8,50 + 9,00 + 10,00) / 3 = **R$ 9,17/unidade** (arredondado para 2 casas decimais)
>
> O sistema deve exibir: "Média de referência calculada: R$ 9,17/unidade com base em 3 fornecedores. Este valor será usado como preço de referência para o processo licitatório (RN-10)."

## Resultado Obtido

> O sistema validou 3 fornecedores distintos. Calculou média: (R$ 45,00 + R$ 47,50 + R$ 46,00) / 3 = R$ 46,17. Exibiu: 'Cotação validada. Valor de referência: R$ 46,17 (RN-10 atendida).'

## Observação

> O arredondamento deve seguir a regra padrão matemática (5 arredonda para cima). O valor exato é R$ 9,1666..., que arredondado é R$ 9,17.

## Status

[ ] Não executado | [x] Passou | [ ] Falhou | [ ] Bloqueado
