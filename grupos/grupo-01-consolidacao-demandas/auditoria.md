# Especificação do Módulo de Auditoria (Log de Rastreabilidade)

Este documento detalha o mecanismo de auditoria do Módulo G01. O objetivo é garantir a total transparência e rastreabilidade das ações realizadas sobre as demandas das secretarias e sobre a geração do DFD.

## 1. Estrutura de Dados (Modelo do Log)

A tabela de auditoria (`log_auditoria`) será responsável por registar todas as mutações de estado no sistema, abrangendo desde a criação inicial até a consolidação final, incluindo alterações no fluxo do processo.

| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_log` | UUID | Identificador único do registo de log. | `550e8400-e29b-...` |
| `data_hora` | Timestamp | Data e hora exatas da ocorrência no servidor. | `2026-06-02 14:30:00` |
| `id_utilizador` | Inteiro/UUID | ID do utilizador (Secretaria ou Analista) que executou a ação. | `1045` |
| `entidade` | String | Tabela ou entidade do sistema afetada. | `Demanda` |
| `id_entidade` | Inteiro/UUID | ID do registo que foi afetado. | `992` |
| `tipo_acao` | Enum | Ação executada (Ver tabela de ações detalhadas abaixo). | `ITEM_FUNDIDO` |
| `dados_anteriores` | JSON | Estado do registo *antes* da modificação (nulo se for criação). | `{"qtd": 50, "status": "Pendente"}` |
| `dados_novos` | JSON | Estado do registo *após* a modificação. | `{"qtd": 30, "status": "Consolidada"}` |

### Tabela de Ações Detalhadas (Enum `tipo_acao`)
O sistema deve rastrear e catalogar a intenção exata da modificação:

* `CRIACAO_INICIAL`: Quando uma Secretaria salva um rascunho ou submete a primeira versão da demanda.
* `EDICAO_ESPECIFICACAO`: Qualquer alteração textual no nome, descrição ou justificativa do material (ex: de "Papel Branco" para "Papel A4 Sulfite").
* `EDICAO_QUANTIDADE`: Qualquer alteração na quantidade solicitada, seja para mais ou para menos.
* `ITEM_FUNDIDO`: Quando o Analista de Compras aplica o ADR 001, juntando dois ou mais itens de secretarias diferentes sob o mesmo nome padronizado.
* `EXCLUSAO_LOGICA`: Quando um item é removido do rascunho de consolidação (o registo não é apagado fisicamente, apenas inativado).
* `GERACAO_DFD`: Quando o rascunho final de consolidação é oficialmente transformado no Documento de Formalização de Demanda.
* `BLOQUEIO_PRAZO`: Ação (geralmente gerada pelo sistema ou manualmente pelo Analista) que impede novas submissões após a data limite.

## 2. Regras de Negócio de Auditoria

* **RN-AUD-01 (Cobertura Total):** Qualquer operação de `INSERT`, `UPDATE` ou `DELETE` lógico (soft delete) nas entidades `Demanda`, `Item`, `DFD` e `PeriodoColeta` deve disparar a criação de um log.
* **RN-AUD-02 (Imutabilidade):** Os registos da tabela `log_auditoria` são de leitura e inserção estrita (*append-only*). O sistema não deve possuir nenhuma funcionalidade, nem mesmo para administradores do banco de dados na interface da aplicação, que permita a edição ou eliminação (delete) de um log.
* **RN-AUD-03 (Obrigatoriedade):** Nenhuma transação pode ser confirmada (*commit*) na base de dados principal se a inserção do log correspondente no módulo de auditoria falhar.
* **RN-AUD-04 (Registo de IP e Sessão):** Além do ID do utilizador, o sistema deve capturar no cabeçalho da requisição o IP de origem, o *User-Agent* (navegador) e anexá-los aos metadados do log (armazenado num campo adicional do `dados_novos`), para efeitos de investigação de segurança cibernética.

## 3. Diagrama de Sequência: Fluxo de Modificação com Auditoria

O diagrama abaixo ilustra o comportamento do sistema quando um Analista de Compras altera a quantidade de um item de uma demanda (exemplo: cortando um excesso de pedido) e como o sistema regista essa ação.

```plantuml
@startuml diagrama-sequencia-auditoria
skinparam DefaultFontName Arial
skinparam sequenceMessageAlign center

actor "Analista de Compras" as Utilizador
participant "Interface (Front-end)" as UI
participant "Controlador / API" as API
participant "Serviço de Demanda" as SvcDemanda
participant "Serviço de Auditoria" as SvcAudit
database "Base de Dados" as BD

Utilizador -> UI : Altera quantidade da Demanda #992 (de 50 para 30)
UI -> API : PUT /demandas/992 \n{ "qtd": 30 }
activate API

API -> SvcDemanda : atualizarQuantidade(992, 30, id_utilizador)
activate SvcDemanda

SvcDemanda -> BD : SELECT * FROM demandas WHERE id = 992
BD --> SvcDemanda : Retorna Demanda (qtd = 50)
note right of SvcDemanda: Guarda os "dados anteriores"

SvcDemanda -> SvcDemanda : Valida nova quantidade
SvcDemanda -> BD : UPDATE demandas SET qtd = 30 WHERE id = 992
note right of SvcDemanda: Mantém a transação aberta

SvcDemanda -> SvcAudit : registarLog("Demanda", 992, "EDICAO_QUANTIDADE", dadosAnt, dadosNovos, id_utilizador)
activate SvcAudit

SvcAudit -> BD : INSERT INTO log_auditoria (...)
BD --> SvcAudit : Sucesso
deactivate SvcAudit

SvcDemanda -> BD : COMMIT (Confirma Transação)
SvcDemanda --> API : Demanda atualizada com sucesso
deactivate SvcDemanda

API --> UI : 200 OK
deactivate API

UI --> Utilizador : Ecrã atualizado com nova quantidade
@enduml