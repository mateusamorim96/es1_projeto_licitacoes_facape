# CT-12 — Iniciar cotação com 2 fornecedores — deve exigir mínimo de 3

## Identificação

| Campo | Valor |
|-------|-------|
| **ID** | CT-12 |
| **User Story** | US-05 |
| **Regra de Negócio** | RN-10 |
| **Prioridade** | Alta |
| **Tipo de Teste** | Negativo |
| **Nível** | Unitário |

## Pré-condições

- O usuário está na etapa de cotação de preços de um processo licitatório
- Apenas 2 fornecedores foram consultados até o momento

## Dados de Entrada

| Campo | Valor |
|-------|-------|
| Item a ser cotado | Caderno universitário |
| Fornecedor 1 | Distribuidora ABC — R$ 8,50/un |
| Fornecedor 2 | Papelaria XYZ — R$ 9,00/un |
| Fornecedor 3 | (não informado) |

## Passos de Execução

1. Acessar o módulo de cotação de preços
2. Registrar proposta do Fornecedor 1 (R$ 8,50)
3. Registrar proposta do Fornecedor 2 (R$ 9,00)
4. Tentar finalizar a cotação com apenas 2 fornecedores
5. Verificar o comportamento do sistema

## Resultado Esperado

> O sistema deve **bloquear** a finalização da cotação e exigir que ao menos um terceiro fornecedor seja consultado. Mensagem esperada: "BLOQUEADO: A cotação exige mínimo de 3 fornecedores distintos. Fornecedores registrados: 2. Adicione ao menos mais 1 fornecedor para prosseguir (RN-10)."

## Resultado Obtido

> O sistema identificou apenas 2 fornecedores na cotação e bloqueou a finalização. Exibiu: 'Mínimo de 3 fornecedores distintos exigido. Falta 1 fornecedor para atender RN-10.'

## Status

[ ] Não executado | [x] Passou | [ ] Falhou | [ ] Bloqueado
