# CT-09 — Emitir OF em ata com validade vencida — deve bloquear com mensagem

## Identificação

| Campo | Valor |
|-------|-------|
| **ID** | CT-09 |
| **User Story** | US-03 |
| **Regra de Negócio** | RN-04 |
| **Prioridade** | Alta |
| **Tipo de Teste** | Negativo |
| **Nível** | Unitário |

## Pré-condições

- Existe uma Ata SRP com prazo de validade expirado (data atual: 2026-05-15, validade da ata: 2026-04-30)
- A ata ainda possui saldo disponível (50 unidades)
- O usuário tenta emitir uma OF com base nessa ata

## Dados de Entrada

| Campo | Valor |
|-------|-------|
| Item da OF | Toner para impressora HP |
| Quantidade solicitada | 10 unidades |
| Saldo da ata | 50 unidades (suficiente) |
| Validade da ata | 2026-04-30 (vencida) |
| Data atual do sistema | 2026-05-15 |

## Passos de Execução

1. Acessar o módulo de geração de OF
2. Selecionar a ata SRP com validade 2026-04-30
3. Informar a quantidade de 10 unidades
4. Tentar confirmar a emissão da OF
5. Verificar se o sistema bloqueia a operação

## Resultado Esperado

> O sistema deve **bloquear** a emissão da OF mesmo que haja saldo suficiente, pois a ata está com a validade vencida. Mensagem esperada: "BLOQUEADO: A ata selecionada venceu em 30/04/2026. Não é possível emitir Ordem de Fornecimento com base em ata fora do prazo de validade (RN-04). Solicite nova licitação ou pesquise atas vigentes."

## Resultado Obtido

> O sistema verificou validade da ata (2026-04-30) versus data atual (2026-05-15) e bloqueou a emissão. Exibiu: 'Ata SRP vencida em 30/04/2026. Não é possível emitir OF com ata vencida (RN-04).' OF não emitida.

## Status

[ ] Não executado | [x] Passou | [ ] Falhou | [ ] Bloqueado
