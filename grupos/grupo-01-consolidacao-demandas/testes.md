# Documento de Especificação de Testes (Módulo G01 - DFD)

Este documento descreve os cenários de **testes unitários** e de **integração** focados na regra de negócio principal do módulo: a validação e a consolidação de demandas das secretarias.

---

## 1. Testes Unitários

Os testes unitários validam o comportamento isolado das funções e métodos do sistema (regras de negócio puras), sem depender de banco de dados ou interfaces.

### 🧪 Caso de Teste 01: Soma de quantidades de itens idênticos
* **Objetivo:** Garantir que o algoritmo soma corretamente a quantidade de itens iguais vindos de diferentes demandas.
* **Entrada Simulada (Mock):** 
  * Objeto Demanda A: `Item("Cartucho de Tinta Preto", qtd=50)`
  * Objeto Demanda B: `Item("Cartucho de Tinta Preto", qtd=30)`
* **Ação:** Executar o método `consolidarItens()`.
* **Resultado Esperado:** O método deve retornar uma lista contendo apenas 1 objeto do tipo `ItemConsolidado` com a quantidade total igual a `80`.

### 🧪 Caso de Teste 02: Bloqueio de demanda com quantidade inválida
* **Objetivo:** Validar a integridade dos dados antes de qualquer consolidação.
* **Entrada Simulada:** Um objeto de demanda para "Papel A4" com a quantidade definida como `0` ou `-5`.
* **Ação:** Executar o método `validarDemanda()`.
* **Resultado Esperado:** O sistema deve falhar a validação, lançando uma exceção de regra de negócio (ex: `QuantidadeInvalidaException`) e impedir que o item siga no fluxo.

### 🧪 Caso de Teste 03: Detecção de similaridade de strings (Baseado no ADR 001)
* **Objetivo:** Verificar se o algoritmo de aproximação textual identifica nomes parecidos para sugerir a fusão.
* **Entrada Simulada:** Strings `"Papel A4 Branco"` e `"Papel Sulfite A4"`.
* **Ação:** Executar o método `calcularSimilaridade(string1, string2)`.
* **Resultado Esperado:** Retornar um score (índice) superior a `0.75` (75%) e marcar o par como "Sugestão de Consolidação".

---

## 2. Testes de Integração

Os testes de integração verificam se os diferentes componentes do sistema (front-end, regras de consolidação, banco de dados e módulo de auditoria) funcionam corretamente em conjunto.

### 🔗 Caso de Teste 04: Fluxo Completo de Consolidação e Mudança de Status
* **Objetivo:** Verificar a integração do processo de ponta a ponta, desde a seleção dos itens até a persistência do DFD no banco.
* **Pré-condições:** 
  * O banco de dados de teste possui a Demanda `#101` (Status: "Pendente", 2 Computadores) e a Demanda `#102` (Status: "Pendente", 3 Computadores).
* **Passos de Execução:**
  1. O ator "Analista de Compras" envia a requisição para consolidar as demandas `#101` e `#102`.
  2. O sistema processa a união (soma = 5).
  3. O sistema gera o documento DFD.
* **Resultado Esperado:** 
  1. O banco de dados deve registrar um novo objeto `DFD` contendo o item consolidado (5 Computadores).
  2. O status das demandas `#101` e `#102` deve ser atualizado obrigatoriamente para `"Consolidada"`.
  3. A tabela de `log_auditoria` deve registrar o ID do Analista de Compras e a ação "GEROU_DFD".

### 🔗 Caso de Teste 05: Integração de Falha na Persistência (Rollback)
* **Objetivo:** Garantir que o sistema não deixe dados inconsistentes caso ocorra um erro de rede ou de banco de dados no meio do processo de geração do DFD.
* **Pré-condições:** Demandas pendentes prontas para consolidação. O banco de dados está configurado para simular uma queda (timeout) no momento de salvar o DFD.
* **Passos de Execução:** Analista tenta consolidar as demandas.
* **Resultado Esperado:** O sistema deve abortar a transação (*rollback*). As demandas originais devem continuar com o status `"Pendente"` (não podem ser marcadas como consolidadas se o DFD não foi salvo com sucesso) e uma mensagem de erro genérica deve ser exibida ao usuário.