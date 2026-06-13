# CT-08 — Emitir OF de 101 unidades com saldo de ata igual a 100 — deve bloquear

## Identificação

| Campo | Valor |
|-------|-------|
| **ID** | CT-08 |
| **User Story** | US-03 |
| **Regra de Negócio** | RN-04 |
| **Prioridade** | Alta |
| **Tipo de Teste** | Negativo |
| **Nível** | Unitário |

## Pré-condições

- Existe uma Ata SRP vigente com saldo disponível de 100 unidades do item "Resma de papel A4"
- A ata está dentro do prazo de validade
- O resultado do pregão foi recebido do G07

## Dados de Entrada

| Campo | Valor |
|-------|-------|
| Item da OF | Resma de papel A4 |
| Quantidade solicitada na OF | 101 unidades |
| Saldo disponível na ata | 100 unidades |
| Validade da ata | 2026-12-31 (vigente) |
| Fornecedor vencedor | Papelaria Nordeste LTDA |

## Passos de Execução

1. Acessar o módulo de geração de OF
2. Selecionar a ata SRP correspondente
3. Informar a quantidade de 101 unidades
4. Tentar confirmar a emissão da OF
5. Verificar se o sistema bloqueia a operação

## Resultado Esperado

> O sistema deve **bloquear** a emissão da OF, pois a quantidade solicitada (101) excede o saldo disponível na ata (100). Mensagem esperada: "BLOQUEADO: Saldo insuficiente na ata. Solicitado: 101 unidades. Disponível: 100 unidades. Reduza a quantidade ou abra novo processo licitatório para o excedente (RN-04)."

## Resultado Obtido

> O sistema detectou quantidade solicitada (101) maior que saldo disponível (100) e bloqueou a emissão. Exibiu: 'Saldo insuficiente: solicitado 101, disponível 100 (RN-04).' OF não emitida, saldo inalterado.

## Status

[ ] Não executado | [x] Passou | [ ] Falhou | [ ] Bloqueado
