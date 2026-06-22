# CT-05 — Criar DFD de Expediente quando já existe outro aberto — sistema deve alertar e mesclar

## Identificação

| Campo | Valor |
|-------|-------|
| **ID** | CT-05 |
| **User Story** | US-02 |
| **Regra de Negócio** | RN-03 |
| **Prioridade** | Alta |
| **Tipo de Teste** | Funcional |
| **Nível** | Integração |

## Pré-condições

- Já existe um DFD aberto no sistema para a categoria "Material de Expediente" no exercício corrente (2026)
- O DFD existente contém 3 itens: papel A4, caneta esferográfica e grampeador
- Um segundo usuário tenta registrar um novo DFD de Expediente com itens diferentes

## Dados de Entrada

| Campo | Valor |
|-------|-------|
| Categoria do novo DFD | Material de Expediente |
| Exercício | 2026 |
| Itens do novo DFD | Tesoura, clips, corretivo |
| DFD existente (ID) | DFD-2026-001 |

## Passos de Execução

1. Acessar o módulo de registro de DFD
2. Iniciar novo DFD com categoria "Material de Expediente" para o exercício 2026
3. Informar os itens: tesoura, clips, corretivo
4. Tentar salvar o DFD
5. Verificar o comportamento do sistema

## Resultado Esperado

> O sistema deve detectar que já existe um DFD aberto para "Material de Expediente" em 2026, exibir um **alerta** informando o conflito e oferecer a opção de **mesclar os itens** ao DFD existente (DFD-2026-001). O sistema não deve criar um segundo DFD paralelo. Mensagem esperada: "Já existe o DFD-2026-001 aberto para Material de Expediente em 2026. Deseja mesclar os itens ao DFD existente? (RN-03 — proibição de fracionamento de despesa)"

## Resultado Obtido

> O sistema detectou o DFD existente para Material de Expediente no exercício 2026, alertou sobre o risco de fracionamento e sugeriu a mesclagem dos itens (RN-03). Criação de DFD duplicado bloqueada com mensagem de alerta.

## Status

[ ] Não executado | [x] Passou | [ ] Falhou | [ ] Bloqueado
