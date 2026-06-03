# CT-10 — Adesão de 50% do quantitativo da ata — deve permitir

## Identificação

| Campo | Valor |
|-------|-------|
| **ID** | CT-10 |
| **User Story** | US-04 |
| **Regra de Negócio** | RN-08 |
| **Prioridade** | Média |
| **Tipo de Teste** | Funcional |
| **Nível** | Unitário |

## Pré-condições

- Existe uma ata SRP de **outro órgão** (ata alheia/carona) com quantitativo original de 200 unidades de cadeiras de escritório
- A FACAPE deseja aderir a essa ata como "carona"
- Nenhuma adesão anterior foi feita a esta ata

## Dados de Entrada

| Campo | Valor |
|-------|-------|
| Item | Cadeira de escritório |
| Quantitativo original da ata | 200 unidades |
| Quantidade solicitada pela FACAPE | 100 unidades (50%) |
| Tipo de adesão | Carona (ata alheia) |

## Passos de Execução

1. Acessar o módulo de pesquisa de atas SRP
2. Selecionar a ata de outro órgão
3. Informar adesão de 100 unidades (50% de 200)
4. Confirmar a solicitação de adesão
5. Verificar se o sistema permite

## Resultado Esperado

> O sistema deve **permitir** a adesão, pois 100 unidades representam exatamente 50% do quantitativo original da ata (200), que é o limite máximo permitido pela RN-08. Confirmação esperada: "Adesão de 100 unidades aprovada. Limite de 50% do quantitativo original (200 unidades) respeitado (RN-08)."

## Resultado Obtido

> O sistema calculou 50% do quantitativo original (200 x 0.50 = 100). Quantidade solicitada = 100, dentro do limite. Adesão registrada com sucesso e evento enviado ao G09 (RN-08).

## Status

[ ] Não executado | [x] Passou | [ ] Falhou | [ ] Bloqueado
