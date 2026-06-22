# ADR 002: Estratégia de Armazenamento para Trilha de Auditoria

* **Data:** 02/06/2026
* **Status:** Aceito
* **Autores:** Grupo 01

## 1. Contexto
As entregas mínimas do projeto exigem um mecanismo de **Auditoria (Log de quem criou/modificou cada demanda)**. Sendo um sistema de compras públicas, o histórico de modificações precisa ser blindado contra adulterações internas e deve rastrear exatamente o estado do dado antes e depois da consolidação (quem cortou quantidades, quem alterou justificativas e quando).

## 2. Decisão
Decidimos adotar o padrão de tabela de **Trilha de Auditoria (Audit Trail)** gerenciada no nível da aplicação dentro do banco de dados relacional. Cada alteração nas entidades `Demanda` e `DFD` disparará um registro em uma tabela separada (`log_auditoria`). 
Esta tabela armazenará: `id_usuario`, `data_hora`, `acao` (CREATE, UPDATE, DELETE), `id_registro_afetado`, e os estados dos dados salvos em campos do tipo **JSON** (`dados_anteriores` e `dados_novos`). A tabela de auditoria será configurada com permissão de escrita (*Append-Only*), impossibilitando atualizações ou exclusões físicas dos logs via aplicação.

## 3. Alternativas Consideradas
* **Logs em Arquivos de Texto (.log):** Descartados porque dificultam a criação de telas de consulta de auditoria dentro do próprio sistema de gerenciamento.
* **Tabelas de Histórico Espelhadas (Ex: demanda_history):** Descartadas porque exigiriam criar uma tabela de histórico para cada entidade nova do sistema, aumentando a manutenção do banco de dados.

## 4. Consequências
### ✅ Consequências Positivas (Ganhos)
* Facilidade para consultar e reconstruir o histórico de qualquer demanda diretamente por comandos SQL.
* O uso de campos JSON isola as mudanças de atributos das entidades; se adicionarmos um campo novo na demanda, a estrutura de logs não quebra.
* Atendimento estrito aos requisitos de conformidade e controle de integridade do setor público.

### ❌ Consequências Negativas (Custos/Riscos)
* O tamanho do banco de dados crescerá de forma acelerada, exigindo políticas futuras de expurgo/arquivamento de logs antigos.
* Pequeno impacto na performance das operações de escrita (escrita dupla: na tabela principal e na tabela de log).