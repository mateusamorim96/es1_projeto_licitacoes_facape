# Backlog — Módulo G07: Acompanhamento de Processo Externo

**Projeto:** Sistema de Licitações — FACAPE  
**Módulo:** G07 — Acompanhamento do Pregão Eletrônico  
**Data:** 2026-06-12  
**Status:** Em elaboração

---

## Épicos

| Código | Épico |
|--------|-------|
| EP-01 | Integração com API PNCP |
| EP-02 | Acompanhamento de Status do Pregão |
| EP-03 | Registro do Resultado |
| EP-04 | Notificações |
| EP-05 | Auditoria e Rastreabilidade |

---

## Histórias de Usuário

---

### EP-01 — Integração com API PNCP

---

#### US-01 — Consultar status do pregão via API

**Como** Analista de Licitações,  
**Quero** que o sistema consulte automaticamente a API do PNCP em intervalos regulares,  
**Para que** eu não precise acessar manualmente o portal para verificar mudanças de status.

**Critérios de Aceitação:**
- O sistema realiza requisições GET à API do PNCP com o número do processo como parâmetro.
- O intervalo de consulta é configurável (padrão: 30 minutos em dias úteis).
- Em caso de erro de rede ou timeout, o sistema registra a falha no log de auditoria e retenta na próxima janela.
- O sistema não realiza mais de uma requisição simultânea para o mesmo processo.

**Prioridade:** Alta  
**Estimativa:** 8 pontos  
**Relacionado a:** UC-01, ADR-002

---

#### US-02 — Autenticar requisições à API do PNCP

**Como** desenvolvedor do módulo G07,  
**Quero** que o sistema utilize as credenciais corretas ao acessar a API do PNCP,  
**Para que** as requisições sejam aceitas e os dados retornados corretamente.

**Critérios de Aceitação:**
- As credenciais (token/chave de API) são armazenadas em variável de ambiente, não em código-fonte.
- O sistema renova automaticamente o token quando este expirar.
- Falhas de autenticação são registradas no log de auditoria com código de erro.

**Prioridade:** Alta  
**Estimativa:** 5 pontos  
**Relacionado a:** ADR-002, ADR-003

---

### EP-02 — Acompanhamento de Status do Pregão

---

#### US-03 — Detectar mudança de status do pregão

**Como** Analista de Licitações,  
**Quero** que o sistema detecte automaticamente quando o status do pregão mudar,  
**Para que** eu seja informado imediatamente sem precisar verificar o portal manualmente.

**Critérios de Aceitação:**
- O sistema compara o status retornado pela API com o último status registrado no banco.
- Qualquer diferença dispara um evento interno de mudança de status.
- Os status suportados são: `ABERTO`, `EM_JULGAMENTO`, `ADJUDICADO`, `FRACASSADO`, `DESERTO`.
- Transições inválidas (ex: de `ADJUDICADO` para `ABERTO`) são rejeitadas e registradas no log.

**Prioridade:** Alta  
**Estimativa:** 8 pontos  
**Relacionado a:** UC-01, UC-04, Classe `StatusPregao`

---

#### US-04 — Atualizar status do pregão manualmente

**Como** Analista de Licitações,  
**Quero** poder atualizar manualmente o status do pregão em casos excepcionais,  
**Para que** o sistema reflita situações que a API pode não capturar automaticamente.

**Critérios de Aceitação:**
- A atualização manual exige autenticação com perfil `LICITACOES`.
- O sistema registra no log de auditoria: usuário, data/hora, status anterior e novo status.
- A atualização manual é marcada com `origem: MANUAL` no registro de `StatusPregao`.
- Não é possível reverter para status anteriores sem aprovação de um segundo usuário autorizado.

**Prioridade:** Média  
**Estimativa:** 5 pontos  
**Relacionado a:** UC-04, ADR-003, Classe `StatusPregao`

---

#### US-05 — Visualizar histórico de status do pregão

**Como** Analista de Licitações ou Auditor,  
**Quero** visualizar o histórico completo de mudanças de status de um pregão,  
**Para que** eu possa acompanhar a evolução do processo e verificar eventuais inconsistências.

**Critérios de Aceitação:**
- A tela exibe todos os registros de `StatusPregao` ordenados por `dataHora` decrescente.
- Cada registro mostra: status, data/hora, origem (API ou MANUAL) e observação.
- O histórico é acessível para os perfis `LICITACOES`, `FISCAL`, `AUDITOR`.
- É possível exportar o histórico em formato CSV.

**Prioridade:** Média  
**Estimativa:** 5 pontos  
**Relacionado a:** UC-05, Classe `StatusPregao`, `LogAuditoria`

---

### EP-03 — Registro do Resultado

---

#### US-06 — Registrar empresa vencedora e valor final

**Como** Analista de Licitações,  
**Quero** que o sistema registre automaticamente a empresa vencedora e o valor final ao detectar status `ADJUDICADO`,  
**Para que** o módulo G08 receba as informações necessárias para emitir a Ordem de Fornecimento.

**Critérios de Aceitação:**
- Ao detectar status `ADJUDICADO` via API, o sistema extrai automaticamente: CNPJ, razão social, valor total adjudicado.
- Os dados são persistidos na entidade `EmpresaVencedora` com vínculo ao `ProcessoExterno`.
- O sistema envia os dados estruturados ao módulo G08 via interface definida.
- Se os dados da API estiverem incompletos, o sistema notifica o Analista para preenchimento manual.

**Prioridade:** Alta  
**Estimativa:** 8 pontos  
**Relacionado a:** UC-02, UC-03, Classes `EmpresaVencedora`, `ResultadoItem`

---

#### US-07 — Validar empresa vencedora

**Como** Analista de Licitações,  
**Quero** que o sistema valide os dados da empresa vencedora (CNPJ, razão social) antes de registrar o resultado,  
**Para que** inconsistências sejam detectadas antes de chegarem ao G08.

**Critérios de Aceitação:**
- O CNPJ é validado pelo dígito verificador antes de ser aceito.
- O sistema verifica se a razão social não está em branco.
- Em caso de inconsistência, o sistema gera um alerta para o Analista de Licitações.
- O resultado somente é enviado ao G08 após validação bem-sucedida.

**Prioridade:** Alta  
**Estimativa:** 5 pontos  
**Relacionado a:** UC-03, Classe `EmpresaVencedora`

---

#### US-08 — Registrar resultado de pregão fracassado ou deserto

**Como** Analista de Licitações,  
**Quero** que o sistema registre corretamente quando o pregão for `FRACASSADO` ou `DESERTO`,  
**Para que** o setor de licitações seja notificado para tomar as providências cabíveis.

**Critérios de Aceitação:**
- O sistema registra o status final com data/hora e motivo (quando disponível na API).
- Notificação é disparada ao perfil `LICITACOES` com o status e o número do processo.
- O sistema não envia dados ao G08 em caso de fracasso ou deserção.
- O registro fica disponível para consulta e auditoria.

**Prioridade:** Alta  
**Estimativa:** 5 pontos  
**Relacionado a:** UC-02, UC-04, ADR-005

---

### EP-04 — Notificações

---

#### US-09 — Notificar usuários por e-mail sobre mudança de status

**Como** Fiscal de Contrato ou Analista de Licitações,  
**Quero** receber um e-mail automático quando o status do pregão mudar,  
**Para que** eu possa tomar as providências necessárias dentro dos prazos legais.

**Critérios de Aceitação:**
- O e-mail é enviado via SMTP ao ocorrer qualquer mudança de status.
- O destinatário é determinado pelo perfil vinculado ao processo (Analista, Fiscal, Gestor).
- O e-mail contém: número do processo, status anterior, novo status, data/hora da mudança.
- Em caso de falha no envio, o sistema retenta até 3 vezes e registra o erro no log.

**Prioridade:** Alta  
**Estimativa:** 5 pontos  
**Relacionado a:** ADR-005, UC-04

---

#### US-10 — Exibir notificações in-app para o usuário logado

**Como** qualquer usuário autenticado no sistema,  
**Quero** ver um banner ou badge na interface quando houver mudança de status em processos que acompanho,  
**Para que** eu seja alertado mesmo sem precisar verificar o e-mail.

**Critérios de Aceitação:**
- Ao fazer login, o usuário vê notificações não lidas com indicador visual (badge).
- Cada notificação exibe: processo, tipo de evento, data/hora.
- O usuário pode marcar notificações como lidas individualmente ou em lote.
- Notificações in-app são persistidas por no mínimo 30 dias.

**Prioridade:** Média  
**Estimativa:** 8 pontos  
**Relacionado a:** ADR-005

---

### EP-05 — Auditoria e Rastreabilidade

---

#### US-11 — Registrar log de cada consulta à API

**Como** Auditor,  
**Quero** que o sistema registre automaticamente cada consulta realizada à API do PNCP,  
**Para que** seja possível rastrear quando cada verificação foi feita e qual foi o resultado.

**Critérios de Aceitação:**
- Cada requisição à API gera um registro em `LogAuditoria` com: timestamp, tipo de ação, usuário/sistema, resultado (sucesso/erro), código HTTP retornado.
- Os logs são imutáveis — não podem ser editados ou excluídos por nenhum perfil.
- Os logs são retidos por no mínimo 5 anos, conforme exigência de rastreabilidade da Lei 14.133/2021.
- O Auditor pode filtrar logs por processo, período e tipo de ação.

**Prioridade:** Alta  
**Estimativa:** 5 pontos  
**Relacionado a:** UC-05, ADR-001, Classe `LogAuditoria`

---

#### US-12 — Consultar log de auditoria por processo

**Como** Auditor,  
**Quero** visualizar todos os registros de auditoria vinculados a um processo específico,  
**Para que** eu possa verificar a integridade e a rastreabilidade das ações realizadas.

**Critérios de Aceitação:**
- A tela de auditoria exibe logs filtrados por número do processo.
- Os registros são ordenados por timestamp decrescente.
- É possível exportar os logs em CSV ou PDF.
- O acesso à tela de auditoria é restrito ao perfil `AUDITOR`.

**Prioridade:** Alta  
**Estimativa:** 5 pontos  
**Relacionado a:** UC-05, ADR-003, Classe `LogAuditoria`

---

## Resumo do Backlog

| ID | Épico | Prioridade | Pontos |
|----|-------|-----------|--------|
| US-01 | EP-01 | Alta | 8 |
| US-02 | EP-01 | Alta | 5 |
| US-03 | EP-02 | Alta | 8 |
| US-04 | EP-02 | Média | 5 |
| US-05 | EP-02 | Média | 5 |
| US-06 | EP-03 | Alta | 8 |
| US-07 | EP-03 | Alta | 5 |
| US-08 | EP-03 | Alta | 5 |
| US-09 | EP-04 | Alta | 5 |
| US-10 | EP-04 | Média | 8 |
| US-11 | EP-05 | Alta | 5 |
| US-12 | EP-05 | Alta | 5 |
| **Total** | | | **72 pts** |

---

*Referências: ADR-001, ADR-002, ADR-003, ADR-004, ADR-005 — Grupo 07 | Lei nº 14.133/2021*
