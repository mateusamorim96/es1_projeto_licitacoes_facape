# CT-02 — Item com valor total acima de R$ 80.000 deve ter cota de 25% para ME/EPP

## Identificação

| Campo | Valor |
|-------|-------|
| **ID** | CT-02 |
| **User Story** | US-01 |
| **Regra de Negócio** | RN-02 |
| **Prioridade** | Alta |
| **Tipo de Teste** | Funcional |
| **Nível** | Unitário |

## Pré-condições

- O sistema possui um item com valor total estimado de R$ 80.001,00
- O processo está em fase de elaboração do TR

## Dados de Entrada

| Campo | Valor |
|-------|-------|
| Descrição do item | Notebook Dell i5 |
| Valor total estimado | R$ 80.001,00 |
| Quantidade total do item | 100 unidades |

## Passos de Execução

1. Acessar o módulo de elaboração do Termo de Referência
2. Informar o item com valor total de R$ 80.001,00 e quantidade de 100 unidades
3. Solicitar ao sistema o cálculo da cota reservada para ME/EPP
4. Verificar o valor calculado

## Resultado Esperado

> O sistema deve calcular e reservar **25 unidades (25%)** do item para ME/EPP, exibindo a mensagem: "Item com valor acima de R$ 80.000,00: 25% do quantitativo (25 unidades) reservado exclusivamente para ME/EPP (RN-02)."

## Resultado Obtido

> O sistema calculou corretamente a cota de 25%: 25 unidades reservadas para ME/EPP. Exibiu a mensagem: "Item com valor acima de R$ 80.000,00: 25% do quantitativo (25 unidades) reservado exclusivamente para ME/EPP (RN-02)."

## Status

[ ] Não executado | [x] Passou | [ ] Falhou | [ ] Bloqueado
