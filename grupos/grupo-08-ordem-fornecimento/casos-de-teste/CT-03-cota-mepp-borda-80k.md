# CT-03 — Item com valor total exatamente R$ 80.000 — caso de borda

## Identificação

| Campo | Valor |
|-------|-------|
| **ID** | CT-03 |
| **User Story** | US-01 |
| **Regra de Negócio** | RN-01, RN-02 |
| **Prioridade** | Alta |
| **Tipo de Teste** | Borda |
| **Nível** | Unitário |

## Pré-condições

- O sistema possui um item com valor total estimado exatamente igual a R$ 80.000,00
- O processo está em fase de elaboração do TR

## Dados de Entrada

| Campo | Valor |
|-------|-------|
| Descrição do item | Impressora laser colorida |
| Valor total estimado | R$ 80.000,00 |
| Quantidade total do item | 40 unidades |

## Passos de Execução

1. Acessar o módulo de elaboração do Termo de Referência
2. Informar o item com valor total de exatamente R$ 80.000,00
3. Verificar qual regra o sistema aplica: exclusividade ME/EPP (RN-01) ou cota de 25% (RN-02)
4. Registrar o comportamento observado

## Resultado Esperado

> Com base na RN-01 ("itens com valor estimado **até** R$ 80.000,00 são exclusivos para ME/EPP"), o sistema deve tratar o valor exato de R$ 80.000,00 como **exclusivo para ME/EPP**, pois o valor não ultrapassa o limite. O sistema deve exibir: "Item classificado como exclusivo ME/EPP (valor igual ao limite de R$ 80.000,00 — RN-01 aplicada)."

## Resultado Obtido

> O sistema aplicou RN-01 e classificou o item como exclusivo ME/EPP, exibindo: "Item classificado como exclusivo ME/EPP (valor igual ao limite de R$ 80.000,00 — RN-01 aplicada)." O operador `<=` foi utilizado corretamente na verificação do limite.

## Observação

> Este é um caso de borda crítico: a implementação deve garantir que o operador `<=` (menor ou igual) seja usado na verificação do limite, e não `<` (menor estrito). Uma implementação incorreta aqui aplicaria RN-02 ao invés de RN-01.

## Status

[ ] Não executado | [x] Passou | [ ] Falhou | [ ] Bloqueado
