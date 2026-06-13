# Plano de Testes — Módulo G07: Acompanhamento de Processo Externo

**Projeto:** Sistema de Licitações — FACAPE  
**Módulo:** G07 — Acompanhamento do Pregão Eletrônico  
**Data:** 2026-06-12  
**Versão:** 1.0

---

## 1. Objetivo

Garantir que o módulo G07 funcione conforme os requisitos definidos nas histórias de usuário, casos de uso e ADRs, validando a integração com a API PNCP, o controle de status, o registro de resultados, as notificações e a auditoria.

---

## 2. Estratégia de Testes

| Tipo | Ferramenta sugerida | Cobertura alvo |
|------|--------------------|----------------|
| Testes unitários | JUnit / Jest / PyTest | 80% das classes de domínio |
| Testes de integração | Postman / REST Assured | Endpoints internos e API PNCP (mock) |
| Testes de sistema | Manual + Selenium (opcional) | Fluxos críticos dos casos de uso |
| Testes de auditoria | SQL + manual | Integridade dos logs gerados |

---

## 3. Casos de Teste

---

### CT-01 — Consulta à API PNCP retorna status válido

**Tipo:** Integração  
**Relacionado a:** US-01, UC-01  
**Pré-condição:** A API PNCP está disponível (ou mock configurado). O sistema possui credenciais válidas.

**Passos:**
1. Configurar mock da API PNCP para retornar `{ "status": "ABERTO" }` para o processo 001/2026.
2. Acionar o job de polling manualmente ou aguardar o intervalo configurado.
3. Verificar o banco de dados.

**Resultado esperado:**
- Um novo registro é criado em `StatusPregao` com `status = ABERTO`, `origem = API`, `dataHora` preenchida.
- Um registro é criado em `LogAuditoria` com `tipoAcao = CONSULTA_API`, `resultado = SUCESSO`.

**Resultado obtido:** _(preencher na execução)_  
**Status:** ⬜ Não executado

---

### CT-02 — Consulta à API PNCP retorna erro de rede

**Tipo:** Integração  
**Relacionado a:** US-01, ADR-002  
**Pré-condição:** Mock da API configurado para simular timeout (sem resposta após 10s).

**Passos:**
1. Configurar mock para retornar timeout.
2. Acionar o job de polling.
3. Verificar o log de auditoria.

**Resultado esperado:**
- Nenhuma alteração em `StatusPregao`.
- Um registro é criado em `LogAuditoria` com `tipoAcao = CONSULTA_API`, `resultado = ERRO`, `observacao = "Timeout na requisição"`.
- O sistema não lança exceção não tratada.

**Resultado obtido:** _(preencher)_  
**Status:** ⬜ Não executado

---

### CT-03 — Detecção de mudança de status (ABERTO → EM_JULGAMENTO)

**Tipo:** Unitário / Integração  
**Relacionado a:** US-03, UC-01, Classe `StatusPregao`  
**Pré-condição:** O banco contém `StatusPregao` com `status = ABERTO` para o processo 001/2026.

**Passos:**
1. Mock da API retorna `{ "status": "EM_JULGAMENTO" }`.
2. Acionar o job de polling.
3. Verificar banco e log.

**Resultado esperado:**
- Novo registro em `StatusPregao` com `status = EM_JULGAMENTO`.
- Log de auditoria registra a mudança.
- Notificação disparada para os usuários vinculados ao processo.

**Resultado obtido:** _(preencher)_  
**Status:** ⬜ Não executado

---

### CT-04 — Rejeição de transição de status inválida

**Tipo:** Unitário  
**Relacionado a:** US-03, Classe `StatusPregao`, método `validarTransicao()`  
**Pré-condição:** Processo está com status `ADJUDICADO`.

**Passos:**
1. Tentar registrar novo status `ABERTO` para o mesmo processo via API interna.
2. Verificar resposta do sistema.

**Resultado esperado:**
- Sistema retorna erro (HTTP 422 ou equivalente).
- Nenhum novo registro é criado em `StatusPregao`.
- Log de auditoria registra a tentativa inválida.

**Resultado obtido:** _(preencher)_  
**Status:** ⬜ Não executado

---

### CT-05 — Registro de empresa vencedora ao detectar ADJUDICADO

**Tipo:** Integração  
**Relacionado a:** US-06, UC-02, UC-03, Classes `EmpresaVencedora`, `ResultadoItem`  
**Pré-condição:** Processo 001/2026 com status `EM_JULGAMENTO`. Mock da API configurado para retornar:
```json
{
  "status": "ADJUDICADO",
  "vencedor": {
    "cnpj": "12.345.678/0001-90",
    "razaoSocial": "Empresa Exemplo LTDA",
    "valorTotal": 15000.00
  }
}
```

**Passos:**
1. Acionar o polling.
2. Verificar banco de dados e interface com G08.

**Resultado esperado:**
- Registro criado em `EmpresaVencedora` com CNPJ, razão social e valor.
- Status atualizado para `ADJUDICADO`.
- Dados enviados ao G08 no formato acordado.
- Log de auditoria registra o evento.

**Resultado obtido:** _(preencher)_  
**Status:** ⬜ Não executado

---

### CT-06 — Validação de CNPJ inválido da empresa vencedora

**Tipo:** Unitário  
**Relacionado a:** US-07, Classe `EmpresaVencedora`  
**Pré-condição:** Sistema em estado `ADJUDICADO`. API retorna CNPJ com dígito verificador incorreto.

**Passos:**
1. Mock retorna `"cnpj": "12.345.678/0001-00"` (dígito verificador inválido).
2. O sistema tenta registrar a empresa vencedora.

**Resultado esperado:**
- Sistema rejeita o CNPJ e não persiste o registro em `EmpresaVencedora`.
- Alerta gerado para o Analista de Licitações.
- Log de auditoria registra inconsistência.
- Dados não são enviados ao G08.

**Resultado obtido:** _(preencher)_  
**Status:** ⬜ Não executado

---

### CT-07 — Pregão fracassado — notificação e ausência de envio ao G08

**Tipo:** Sistema  
**Relacionado a:** US-08, UC-04, ADR-005  
**Pré-condição:** Processo 001/2026 com status `EM_JULGAMENTO`.

**Passos:**
1. Mock da API retorna `{ "status": "FRACASSADO" }`.
2. Acionar polling.
3. Verificar notificações e banco.

**Resultado esperado:**
- Status atualizado para `FRACASSADO`.
- Notificação por e-mail enviada ao perfil `LICITACOES`.
- Notificação in-app criada para o Analista responsável.
- Nenhum dado enviado ao G08.
- Log de auditoria registra o evento.

**Resultado obtido:** _(preencher)_  
**Status:** ⬜ Não executado

---

### CT-08 — Envio de e-mail de notificação de mudança de status

**Tipo:** Integração  
**Relacionado a:** US-09, ADR-005  
**Pré-condição:** SMTP configurado com servidor de teste (ex: Mailtrap). Usuário analista@facape.br vinculado ao processo.

**Passos:**
1. Simular mudança de status de `ABERTO` para `EM_JULGAMENTO`.
2. Verificar caixa do servidor SMTP de teste.

**Resultado esperado:**
- E-mail recebido em analista@facape.br com assunto contendo número do processo e novo status.
- Corpo do e-mail contém: processo, status anterior, novo status, data/hora.
- Log de auditoria registra `tipoAcao = NOTIFICACAO_EMAIL`, `resultado = SUCESSO`.

**Resultado obtido:** _(preencher)_  
**Status:** ⬜ Não executado

---

### CT-09 — Falha no envio de e-mail — retentativa e log de erro

**Tipo:** Integração  
**Relacionado a:** US-09, ADR-005  
**Pré-condição:** SMTP configurado com servidor indisponível.

**Passos:**
1. Simular mudança de status.
2. Verificar comportamento do sistema após 3 tentativas falhas.

**Resultado esperado:**
- O sistema tenta enviar o e-mail 3 vezes.
- Após a 3ª falha, registra em `LogAuditoria`: `tipoAcao = NOTIFICACAO_EMAIL`, `resultado = ERRO`.
- O sistema não lança exceção não tratada e continua operando normalmente.

**Resultado obtido:** _(preencher)_  
**Status:** ⬜ Não executado

---

### CT-10 — Imutabilidade dos logs de auditoria

**Tipo:** Auditoria / Segurança  
**Relacionado a:** US-11, ADR-001, Classe `LogAuditoria`  
**Pré-condição:** Existe ao menos um registro em `LogAuditoria`.

**Passos:**
1. Tentar executar `UPDATE log_auditoria SET resultado = 'SUCESSO' WHERE id = 1` diretamente no banco.
2. Tentar deletar um registro via API com perfil `LICITACOES` ou `AUDITOR`.

**Resultado esperado:**
- O banco rejeita o UPDATE (via constraint ou trigger configurado).
- A API retorna HTTP 403 para tentativas de deleção.
- Nenhum registro é modificado.

**Resultado obtido:** _(preencher)_  
**Status:** ⬜ Não executado

---

### CT-11 — Controle de acesso por perfil (RBAC)

**Tipo:** Segurança / Sistema  
**Relacionado a:** ADR-003, US-04, US-12  
**Pré-condição:** Usuário autenticado com perfil `DEMANDANTE` (não tem acesso à auditoria ou atualização manual).

**Passos:**
1. Autenticar com perfil `DEMANDANTE`.
2. Tentar acessar o endpoint de atualização manual de status (UC-04).
3. Tentar acessar o endpoint de log de auditoria (UC-05).

**Resultado esperado:**
- Ambas as requisições retornam HTTP 403 Forbidden.
- Nenhum dado é retornado ao usuário.
- A tentativa de acesso não autorizado é registrada em `LogAuditoria`.

**Resultado obtido:** _(preencher)_  
**Status:** ⬜ Não executado

---

### CT-12 — Recebimento de edital do G06 e criação de ProcessoExterno

**Tipo:** Integração (interface entre módulos)  
**Relacionado a:** README G07 — Entrada ← G06  
**Pré-condição:** Mock do G06 configurado para enviar payload de edital válido.

**Passos:**
1. G06 envia payload com número do processo, data de publicação e referência do edital.
2. Verificar banco de dados do G07.

**Resultado esperado:**
- Registro criado em `ProcessoExterno` com os dados recebidos.
- Status inicial definido como `ABERTO`.
- Log de auditoria registra a criação do processo.
- Job de polling é agendado para o processo.

**Resultado obtido:** _(preencher)_  
**Status:** ⬜ Não executado

---

## 4. Matriz de Rastreabilidade

| Caso de Teste | História de Usuário | Caso de Uso | ADR |
|--------------|--------------------|-----------|----|
| CT-01 | US-01 | UC-01 | ADR-002 |
| CT-02 | US-01 | UC-01 | ADR-002 |
| CT-03 | US-03 | UC-01, UC-04 | ADR-002 |
| CT-04 | US-03 | UC-04 | ADR-001 |
| CT-05 | US-06 | UC-02, UC-03 | ADR-002 |
| CT-06 | US-07 | UC-03 | — |
| CT-07 | US-08 | UC-02, UC-04 | ADR-005 |
| CT-08 | US-09 | UC-04 | ADR-005 |
| CT-09 | US-09 | UC-04 | ADR-005 |
| CT-10 | US-11 | UC-05 | ADR-001 |
| CT-11 | US-04, US-12 | UC-04, UC-05 | ADR-003 |
| CT-12 | US-01, US-06 | UC-01 | ADR-002 |

---

## 5. Critérios de Aceite Geral

- Todos os casos de teste de prioridade Alta devem passar antes da entrega.
- Cobertura de testes unitários ≥ 80% nas classes: `ProcessoExterno`, `StatusPregao`, `EmpresaVencedora`, `ResultadoItem`, `LogAuditoria`.
- Nenhuma falha crítica aberta nos testes de segurança (CT-10, CT-11).
- Logs de auditoria gerados em 100% das ações testadas.

---

*Referências: ADR-001 a ADR-005, Backlog G07, Lei nº 14.133/2021*
