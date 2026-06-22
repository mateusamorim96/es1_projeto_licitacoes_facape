# CT-07 — Emitir OF de 100 unidades com saldo de ata igual a 100 — deve permitir

## Identificação

| Campo | Valor |
|-------|-------|
| **ID** | CT-07 |
| **User Story** | US-03 |
| **Regra de Negócio** | RN-04 |
| **Prioridade** | Alta |
| **Tipo de Teste** | Funcional |
| **Nível** | Unitário |

## Pré-condições

- Existe uma Ata SRP vigente com saldo disponível de exatamente 100 unidades do item "Resma de papel A4"
- A ata está dentro do prazo de validade (12 meses)
- O resultado do pregão foi recebido do G07 com empresa vencedora definida

## Dados de Entrada

| Campo | Valor |
|-------|-------|
| Item da OF | Resma de papel A4 |
| Quantidade solicitada na OF | 100 unidades |
| Saldo disponível na ata | 100 unidades |
| Validade da ata | 2026-12-31 (vigente) |
| Fornecedor vencedor | Papelaria Nordeste LTDA |

## Passos de Execução

1. Acessar o módulo de geração de OF
2. Selecionar a ata SRP correspondente
3. Informar a quantidade de 100 unidades
4. Confirmar a emissão da OF
5. Verificar se o sistema permite a emissão

## Resultado Esperado

> O sistema deve **permitir** a emissão da OF normalmente, pois a quantidade solicitada é exatamente igual ao saldo disponível. A OF deve ser gerada com número sequencial, data de emissão, itens, valores e prazo de entrega. O saldo da ata deve ser atualizado para **0 unidades**.

## Resultado Obtido

> O sistema validou saldo disponível = 100 e quantidade solicitada = 100. Emissão autorizada. OF gerada com número OF-2026-0001, saldo deduzido para zero, status alterado para EMITIDA (RN-04).

## Status

[ ] Não executado | [x] Passou | [ ] Falhou | [ ] Bloqueado
