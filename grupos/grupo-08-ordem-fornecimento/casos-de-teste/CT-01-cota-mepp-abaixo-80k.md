# CT-01 — Item com valor total abaixo de R$ 80.000 deve ser exclusivo ME/EPP

## Identificação

| Campo | Valor |
|-------|-------|
| **ID** | CT-01 |
| **User Story** | US-01 |
| **Regra de Negócio** | RN-01 |
| **Prioridade** | Alta |
| **Tipo de Teste** | Funcional |
| **Nível** | Unitário |

## Pré-condições

- O sistema possui um item cadastrado com valor total estimado de R$ 79.999,00
- O processo de licitação está em fase de elaboração do TR

## Dados de Entrada

| Campo | Valor |
|-------|-------|
| Descrição do item | Resma de papel A4 |
| Valor total estimado | R$ 79.999,00 |
| Tipo de empresa participante | Empresa de grande porte |

## Passos de Execução

1. Acessar o módulo de geração de OF
2. Informar o item com valor total de R$ 79.999,00
3. Tentar associar uma empresa de grande porte ao item
4. Verificar a resposta do sistema

## Resultado Esperado

> O sistema deve classificar o item como **exclusivo para ME/EPP** e bloquear a participação de empresa de grande porte, exibindo a mensagem: "Item com valor estimado até R$ 80.000,00 é exclusivo para Microempresas e Empresas de Pequeno Porte (Lei Complementar 123/2006 — RN-01)."

## Resultado Obtido

> O sistema classificou o item como exclusivo ME/EPP e exibiu a mensagem: "Item com valor estimado até R$ 80.000,00 é exclusivo para Microempresas e Empresas de Pequeno Porte (Lei Complementar 123/2006 — RN-01)." A participação de empresa de grande porte foi bloqueada conforme esperado.

## Status

[ ] Não executado | [x] Passou | [ ] Falhou | [ ] Bloqueado
