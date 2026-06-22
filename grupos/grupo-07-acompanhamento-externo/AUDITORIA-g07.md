# Auditoria — Módulo G07: Acompanhamento de Processo Externo

**Projeto:** Sistema de Licitações — FACAPE  
**Módulo:** G07 — Acompanhamento do Pregão Eletrônico  
**Data:** 2026-06-12  
**Versão:** 1.0

---

## 1. Objetivo

Definir a estratégia de auditoria do módulo G07, especificando quais eventos são auditados, quais dados são registrados, quem pode consultar os logs, por quanto tempo são retidos e como garantir sua integridade — em conformidade com a Lei nº 14.133/2021.

---

## 2. Fundamento Legal

A Lei nº 14.133/2021 exige transparência, publicidade e rastreabilidade de todos os atos do processo licitatório. Em especial:

- **Art. 7º:** Publicidade e integridade dos atos administrativos.
- **Art. 11, IV:** Dever de transparência na gestão dos recursos públicos.
- **Art. 54:** Obrigatoriedade de publicação no Portal Nacional de Contratações Públicas.

O módulo G07, ao intermediar a integração com o PNCP e registrar os resultados do pregão, é parte crítica dessa cadeia de rastreabilidade.

---

## 3. Modelo de Dados do Log de Auditoria

Cada evento auditável gera um registro na entidade `LogAuditoria`, correspondente à classe identificada no diagrama de classes do grupo.

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id` | UUID | Identificador único imutável do registro |
| `processoId` | UUID | Referência ao `ProcessoExterno` relacionado |
| `timestamp` | DateTime | Data e hora exata do evento (UTC) |
| `tipoAcao` | Enum | Tipo do evento auditado (ver Seção 4) |
| `usuarioOuSistema` | String | Identificação do ator: login do usuário ou `"SISTEMA"` para ações automáticas |
| `resultado` | Enum | `SUCESSO` ou `ERRO` |
| `detalhes` | String | Informações adicionais (ex: status anterior, novo status, código HTTP, mensagem de erro) |
| `ipOrigem` | String | IP da requisição (para ações humanas) |

**Imutabilidade:** Nenhum campo do `LogAuditoria` pode ser alterado ou excluído após a criação. Isso é garantido por:
- Ausência de endpoints de UPDATE/DELETE para essa entidade.
- Constraint de banco de dados (trigger ou política de permissão).
- Perfil `AUDITOR` tem acesso somente leitura à tabela.

---

## 4. Eventos Auditáveis

Todos os eventos abaixo devem gerar obrigatoriamente um registro em `LogAuditoria`.

### 4.1 Ações Automáticas do Sistema

| Código | Evento | `tipoAcao` | Detalhes registrados |
|--------|--------|-----------|---------------------|
| EVT-01 | Consulta periódica à API PNCP | `CONSULTA_API` | Código HTTP, status retornado, tempo de resposta |
| EVT-02 | Detecção de mudança de status | `MUDANCA_STATUS` | Status anterior, novo status, origem `API` |
| EVT-03 | Registro automático de empresa vencedora | `REGISTRO_VENCEDOR` | CNPJ, razão social, valor total |
| EVT-04 | Falha na consulta à API | `ERRO_CONSULTA_API` | Código de erro, mensagem, número de tentativas |
| EVT-05 | Envio de notificação por e-mail | `NOTIFICACAO_EMAIL` | Destinatário, assunto, resultado do envio |
| EVT-06 | Falha no envio de notificação | `ERRO_NOTIFICACAO` | Canal, destinatário, mensagem de erro |
| EVT-07 | Recebimento de edital do G06 | `RECEPCAO_EDITAL` | Número do processo, data de publicação |
| EVT-08 | Envio de resultado ao G08 | `ENVIO_G08` | Dados enviados, resultado da operação |

### 4.2 Ações Humanas

| Código | Evento | `tipoAcao` | Detalhes registrados |
|--------|--------|-----------|---------------------|
| EVT-09 | Login no sistema | `LOGIN` | Usuário, IP, resultado (sucesso/falha) |
| EVT-10 | Atualização manual de status | `ATUALIZACAO_MANUAL_STATUS` | Status anterior, novo status, justificativa |
| EVT-11 | Consulta ao log de auditoria | `CONSULTA_LOG` | Filtros aplicados, período consultado |
| EVT-12 | Tentativa de acesso não autorizado | `ACESSO_NEGADO` | Endpoint acessado, perfil do usuário |
| EVT-13 | Exportação de dados (CSV/PDF) | `EXPORTACAO` | Tipo de relatório, período, usuário |

---

## 5. Controle de Acesso aos Logs

| Perfil | Pode consultar logs | Pode exportar logs | Pode modificar logs |
|--------|--------------------|--------------------|---------------------|
| `AUDITOR` | ✅ Sim (todos os processos) | ✅ Sim | ❌ Nunca |
| `LICITACOES` | ✅ Sim (somente seus processos) | ✅ Sim | ❌ Nunca |
| `FISCAL` | ✅ Sim (somente seus processos) | ❌ Não | ❌ Nunca |
| `DEMANDANTE` | ❌ Não | ❌ Não | ❌ Nunca |
| `CONTABILIDADE` | ✅ Sim (somente valores finais) | ❌ Não | ❌ Nunca |
| `SISTEMA` | — (apenas grava) | — | ❌ Nunca |

---

## 6. Retenção e Backup

| Aspecto | Definição |
|---------|-----------|
| **Período de retenção** | Mínimo de 5 anos (alinhado à prescrição de atos administrativos) |
| **Backup** | Diário, com retenção de 30 dias de backups incrementais e 12 meses de backups completos mensais |
| **Integridade** | Hash SHA-256 calculado sobre o conjunto de logs do dia e armazenado separadamente para verificação |
| **Localização** | Banco de dados relacional (PostgreSQL), mesmo servidor da aplicação (ADR-004) |

---

## 7. Relatório de Auditoria — Estrutura

Quando o Auditor exporta os dados de um processo, o relatório gerado deve conter:

**Cabeçalho:**
- Número do processo licitatório
- Período consultado
- Data e hora de geração do relatório
- Usuário que gerou o relatório

**Corpo (tabela por evento):**
- Timestamp
- Tipo de ação
- Usuário ou Sistema
- Resultado
- Detalhes

**Rodapé:**
- Total de registros exportados
- Hash SHA-256 do arquivo gerado (para verificação de integridade posterior)

---

## 8. Exemplo de Registro de Auditoria

Abaixo, uma sequência de registros que exemplifica um fluxo completo de acompanhamento:

| # | Timestamp | Tipo de Ação | Ator | Resultado | Detalhes |
|---|-----------|-------------|------|-----------|----------|
| 1 | 2026-05-10 08:00:02 | `RECEPCAO_EDITAL` | SISTEMA | SUCESSO | Processo 001/2026 recebido do G06 |
| 2 | 2026-05-10 08:30:01 | `CONSULTA_API` | SISTEMA | SUCESSO | HTTP 200 — status: ABERTO |
| 3 | 2026-05-10 09:00:01 | `CONSULTA_API` | SISTEMA | SUCESSO | HTTP 200 — status: ABERTO (sem mudança) |
| 4 | 2026-05-11 14:30:02 | `CONSULTA_API` | SISTEMA | SUCESSO | HTTP 200 — status: EM_JULGAMENTO |
| 5 | 2026-05-11 14:30:03 | `MUDANCA_STATUS` | SISTEMA | SUCESSO | ABERTO → EM_JULGAMENTO |
| 6 | 2026-05-11 14:30:05 | `NOTIFICACAO_EMAIL` | SISTEMA | SUCESSO | analista@facape.br — mudança de status |
| 7 | 2026-05-12 10:15:02 | `CONSULTA_API` | SISTEMA | SUCESSO | HTTP 200 — status: ADJUDICADO |
| 8 | 2026-05-12 10:15:03 | `MUDANCA_STATUS` | SISTEMA | SUCESSO | EM_JULGAMENTO → ADJUDICADO |
| 9 | 2026-05-12 10:15:04 | `REGISTRO_VENCEDOR` | SISTEMA | SUCESSO | CNPJ: 12.345.678/0001-90 — R$ 15.000,00 |
| 10 | 2026-05-12 10:15:06 | `ENVIO_G08` | SISTEMA | SUCESSO | Resultado enviado ao módulo G08 |
| 11 | 2026-05-12 10:30:10 | `NOTIFICACAO_EMAIL` | SISTEMA | SUCESSO | analista@facape.br, fiscal@facape.br |
| 12 | 2026-05-13 09:00:00 | `CONSULTA_LOG` | auditor@facape.br | SUCESSO | Processo 001/2026, período: 10–12/05/2026 |

---

## 9. Conformidade com a Lei 14.133/2021

| Requisito legal | Como o G07 atende |
|----------------|------------------|
| Transparência dos atos | Todos os eventos são registrados com timestamp e identificação do ator |
| Integridade documental | Logs imutáveis + hash SHA-256 dos arquivos exportados |
| Rastreabilidade do acompanhamento | `LogAuditoria` registra cada consulta ao PNCP com resultado |
| Controle de acesso | RBAC com JWT — somente perfis autorizados acessam dados sensíveis |
| Retenção de documentos | Logs retidos por no mínimo 5 anos |
| Publicidade | Auditor institucional tem acesso irrestrito aos logs do módulo |

---

*Referências:*  
- [Lei nº 14.133/2021](https://www.planalto.gov.br/ccivil_03/_ato2019-2022/2021/lei/l14133.htm)  
- ADR-001 (Estilo Arquitetural), ADR-003 (Autenticação), ADR-004 (Armazenamento)  
- Diagrama de Classes G07 — Classe `LogAuditoria`
