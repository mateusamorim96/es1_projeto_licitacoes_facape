# Auditoria — Módulo: Mapa de Riscos


## Informações registradas em cada evento

Toda ação relevante no módulo gera um registro com os seguintes campos:

| Campo | Descrição | Exemplo |
|-------|-----------|---------|
| `usuario` | Nome e matrícula de quem executou a ação | Chefe de Licitações — mat. 0042 |
| `dataHora` | Data e hora exatas da ação | 2026-05-20 14:35:22 |
| `versaoMapa` | Número da versão do mapa no momento da ação | v1 |
| `statusAnterior` | Status do mapa antes da ação | EM_ELABORACAO |
| `statusNovo` | Status do mapa após a ação | AGUARDANDO_APROVACAO |
| `descricao` | Descrição do que foi feito | Risco R-003 cadastrado com score 15 (Crítico) |

---

## Eventos auditados

### Evento 1 — Risco cadastrado

**Quando ocorre:** ao cadastrar um novo risco no mapa.

| Campo | Valor registrado |
|-------|-----------------|
| Quem | Chefe de Licitações |
| O que | Cadastro de risco com descrição, categoria, probabilidade, impacto e score calculado |
| Quando | Data e hora do cadastro |
| Versão | Versão atual do mapa |

---

### Evento 2 — Mapa submetido para aprovação

**Quando ocorre:** quando o Chefe de Licitações submete o mapa.

| Campo | Valor registrado |
|-------|-----------------|
| Quem | Chefe de Licitações |
| O que | Submissão do mapa com total de riscos e total de riscos críticos |
| Quando | Data e hora da submissão |
| Versão | Versão submetida |

---

### Evento 3 — Avaliação jurídica registrada

**Quando ocorre:** quando o Jurídico Interno registra parecer sobre riscos críticos (score ≥ 15).

| Campo | Valor registrado |
|-------|-----------------|
| Quem | Jurídico Interno |
| O que | Parecer favorável ou desfavorável com justificativa |
| Quando | Data e hora do parecer |
| Versão | Versão em análise |

---

### Evento 4 — Mapa aprovado

**Quando ocorre:** quando o Chefe de Compras aprova o mapa.

| Campo | Valor registrado |
|-------|-----------------|
| Quem | Chefe de Compras |
| O que | Aprovação final — mapa se torna imutável |
| Quando | Data e hora da aprovação |
| Versão | Versão aprovada (arquivada como imutável — RN-MR-07) |

---

### Evento 5 — Mapa rejeitado ou revisão solicitada

**Quando ocorre:** quando Chefe de Compras ou Jurídico rejeita o mapa.

| Campo | Valor registrado |
|-------|-----------------|
| Quem | Chefe de Compras ou Jurídico Interno |
| O que | Motivo da rejeição e lista de pendências apontadas |
| Quando | Data e hora da rejeição |
| Versão | Versão rejeitada |

---

## Exemplo de trilha de auditoria completa

Abaixo está um exemplo de como a trilha de auditoria de um processo licitatório ficaria registrada do início ao fim:

| # | Data/Hora | Usuário | Ação | Versão |
|---|-----------|---------|------|--------|
| 1 | 2026-05-20 09:10 | Chefe de Licitações — mat. 0042 | Risco R-001 cadastrado (Preço elevado, score 12 — Alto) | v1 |
| 2 | 2026-05-20 09:25 | Chefe de Licitações — mat. 0042 | Risco R-002 cadastrado (Especificação inadequada, score 15 — Crítico) | v1 |
| 3 | 2026-05-20 09:40 | Chefe de Licitações — mat. 0042 | Mitigação definida para R-001 (Preventiva) | v1 |
| 4 | 2026-05-20 09:50 | Chefe de Licitações — mat. 0042 | Mitigação definida para R-002 (Contingencial) | v1 |
| 5 | 2026-05-20 10:00 | Chefe de Licitações — mat. 0042 | Mapa submetido para aprovação | v1 |
| 6 | 2026-05-20 10:15 | Jurídico Interno — mat. 0078 | Parecer favorável registrado para R-002 (score ≥ 15) | v1 |
| 7 | 2026-05-20 10:30 | Chefe de Compras — mat. 0010 | Mapa aprovado — versão v1 arquivada como imutável | v1 |

---

## Regras de auditoria

| Regra | Descrição |
|-------|-----------|
| Registro automático | Todo evento é registrado pelo sistema sem depender de ação manual do usuário |
| Imutabilidade dos logs | Registros de auditoria não podem ser editados ou excluídos |
| Rastreabilidade por versão | Cada registro está vinculado à versão do mapa ativa no momento |
| Integração com G09 | Todos os eventos são enviados ao módulo de Auditoria Centralizada (G09) |
