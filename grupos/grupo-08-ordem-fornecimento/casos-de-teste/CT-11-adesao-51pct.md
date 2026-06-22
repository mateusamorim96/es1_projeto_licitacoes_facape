# CT-11 — Adesão de 51% do quantitativo da ata — deve bloquear

## Identificação

| Campo | Valor |
|-------|-------|
| **ID** | CT-11 |
| **User Story** | US-04 |
| **Regra de Negócio** | RN-08 |
| **Prioridade** | Média |
| **Tipo de Teste** | Negativo |
| **Nível** | Unitário |

## Pré-condições

- Existe uma ata SRP de outro órgão com quantitativo original de 200 unidades
- A FACAPE tenta aderir como "carona" com quantidade acima do limite de 50%

## Dados de Entrada

| Campo | Valor |
|-------|-------|
| Item | Cadeira de escritório |
| Quantitativo original da ata | 200 unidades |
| Quantidade solicitada pela FACAPE | 102 unidades (51%) |
| Tipo de adesão | Carona (ata alheia) |

## Passos de Execução

1. Acessar o módulo de pesquisa de atas SRP
2. Selecionar a ata de outro órgão
3. Informar adesão de 102 unidades (51% de 200)
4. Tentar confirmar a solicitação de adesão
5. Verificar se o sistema bloqueia

## Resultado Esperado

> O sistema deve **bloquear** a adesão, pois 102 unidades representam 51% do quantitativo original (200), ultrapassando o limite legal de 50% para adesão a ata alheia. Mensagem esperada: "BLOQUEADO: A quantidade solicitada (102 unidades) excede 50% do quantitativo original da ata (200 unidades). Limite máximo permitido: 100 unidades (RN-08 — adesão/carona limitada a 50%)."

## Resultado Obtido

> O sistema detectou que 102 unidades (51% de 200) ultrapassa o limite de 50% e bloqueou a adesão. Exibiu: 'Limite de adesão excedido: máximo disponível é 100 unidades (50% de 200 — RN-08).'

## Status

[ ] Não executado | [x] Passou | [ ] Falhou | [ ] Bloqueado
