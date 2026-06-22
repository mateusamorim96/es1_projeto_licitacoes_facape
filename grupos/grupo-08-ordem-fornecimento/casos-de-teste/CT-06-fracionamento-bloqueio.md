# CT-06 — Criar dois processos de expediente no mesmo exercício — sistema deve bloquear

## Identificação

| Campo | Valor |
|-------|-------|
| **ID** | CT-06 |
| **User Story** | US-02 |
| **Regra de Negócio** | RN-03 |
| **Prioridade** | Alta |
| **Tipo de Teste** | Negativo |
| **Nível** | Unitário |

## Pré-condições

- Já existe um processo licitatório **finalizado e homologado** para "Material de Expediente" no exercício 2026
- O usuário tenta abrir um segundo processo independente para a mesma categoria no mesmo exercício

## Dados de Entrada

| Campo | Valor |
|-------|-------|
| Categoria | Material de Expediente |
| Exercício | 2026 |
| Processo existente | PROC-2026-015 (homologado) |
| Novo processo solicitado | Material de Expediente — itens adicionais |

## Passos de Execução

1. Acessar o módulo de abertura de processo licitatório
2. Selecionar a categoria "Material de Expediente" para o exercício 2026
3. Informar que se trata de uma nova licitação independente
4. Tentar salvar e prosseguir
5. Verificar o comportamento do sistema

## Resultado Esperado

> O sistema deve **bloquear completamente** a criação de um segundo processo independente para a mesma categoria no mesmo exercício, exibindo: "BLOQUEADO: Já existe o processo PROC-2026-015 para Material de Expediente em 2026. A criação de um segundo processo independente configura fracionamento de despesa, vedado pela RN-03 e pela Lei 14.133/2021. Utilize o processo existente ou justifique tecnicamente a necessidade de novo processo."

## Resultado Obtido

> O sistema identificou o processo homologado para Material de Expediente em 2026 e bloqueou completamente a criação de novo processo para a mesma categoria no mesmo exercício, exibindo o número do processo existente (RN-03).

## Status

[ ] Não executado | [x] Passou | [ ] Falhou | [ ] Bloqueado
