# ADRs — Decisões de Arquitetura e Modelagem

## ADR-01 — Qual matriz de risco usar: 3×3, 4×4 ou 5×5?

**Data:** 2026-05
**Status:** Aprovado

### Contexto

O módulo G05 precisa calcular o nível de criticidade de cada risco a partir de dois valores: probabilidade e impacto. Existem diferentes escalas usadas no mercado. Era necessário escolher uma antes de modelar o diagrama de classes e os testes.

### Alternativas consideradas

| Alternativa | Escala | Scores possíveis | Prós | Contras |
|-------------|--------|-----------------|------|---------|
| Matriz 3×3 | Baixo / Médio / Alto | 1, 2, 3, 4, 6, 9 | Simples, fácil de preencher | Pouca granularidade — riscos diferentes ficam no mesmo nível |
| Matriz 4×4 | 1 a 4 | 1 a 16 | Equilíbrio entre simplicidade e detalhe | Pouco usada em órgãos públicos brasileiros |
| **Matriz 5×5** | **1 a 5** | **1 a 25** | Padrão TCU e maioria dos órgãos públicos brasileiros | Exige mais cuidado do usuário ao classificar |

### Decisão

**Adotamos a Matriz 5×5** com escala de 1 a 5 para probabilidade e para impacto.

O score é calculado como: `score = probabilidade × impacto`

A classificação dos níveis segue:

| Score | Nível | Ação exigida |
|-------|-------|-------------|
| 1–4 | Baixo | Monitoramento simples |
| 5–9 | Médio | Mitigação planejada |
| 10–14 | Alto | Mitigação obrigatória |
| 15–25 | Crítico | Mitigação obrigatória + validação do Jurídico Interno (RN-MR-03) |

### Justificativa

A matriz 5×5 é o padrão adotado pelo TCU (Tribunal de Contas da União) e pela maioria dos órgãos públicos brasileiros em auditorias de gestão de riscos. Oferece granularidade suficiente para diferenciar riscos intermediários sem complexidade excessiva. O limiar objetivo de score ≥ 15 para riscos críticos também permite automatizar a regra de validação jurídica (RN-MR-03) sem ambiguidade.

---

## ADR-02 — Os atores do módulo são os definidos pelo professor ou os da FACAPE?

**Data:** 2026-05
**Status:** Aprovado

### Contexto

O documento do professor define os atores do G05 como "Analista de Risco" e "Especialista Técnico". Porém, no contexto real da FACAPE levantado nas aulas, quem executa as tarefas do Mapa de Riscos são o Chefe de Licitações, o Chefe de Compras e o Jurídico Interno.

### Alternativas consideradas

| Alternativa | Atores | Prós | Contras |
|-------------|--------|------|---------|
| Seguir literalmente o professor | Analista de Risco, Especialista Técnico | Aderência estrita ao enunciado | Não reflete a realidade da FACAPE descrita no contexto do sistema |
| **Usar atores reais da FACAPE** | **Chefe de Licitações, Chefe de Compras, Jurídico Interno** | Coerente com o domínio real levantado nas aulas | Diverge dos nomes do enunciado |

### Decisão

**Adotamos os atores reais da FACAPE:** Chefe de Licitações, Chefe de Compras e Jurídico Interno.

### Justificativa

O documento `contexto-do-sistema.md` do repositório descreve explicitamente esses três papéis como os responsáveis pela fase interna do processo licitatório. O professor validou esse contexto em sala. Usar "Analista de Risco" e "Especialista Técnico" criaria inconsistência com todos os outros artefatos do projeto que usam os papéis reais. Os nomes do enunciado são genéricos e equivalem funcionalmente aos cargos reais da FACAPE.

---

## ADR-03 — Como tratar riscos críticos: bloquear aprovação ou apenas alertar?

**Data:** 2026-05
**Status:** Aprovado

### Contexto

Quando um risco tem score ≥ 15 (crítico), o sistema precisa decidir: bloqueia a aprovação e força a validação jurídica, ou apenas exibe um alerta e deixa o Chefe de Compras decidir?

### Alternativas consideradas

| Alternativa | Comportamento | Prós | Contras |
|-------------|--------------|------|---------|
| Apenas alertar | Exibe aviso, mas permite aprovar sem Jurídico | Mais flexível | Viola a Lei 14.133/2021 — riscos graves sem análise legal |
| **Bloquear e exigir validação** | **Jurídico deve validar antes da aprovação** | Garante conformidade legal (RN-MR-03) | Adiciona uma etapa ao fluxo |

### Decisão

**O sistema bloqueia a aprovação** e exige a validação do Jurídico Interno quando há risco com score ≥ 15, conforme modelado no BPMN e nos diagramas de sequência.

### Justificativa

A Lei 14.133/2021 exige que riscos graves em contratações públicas sejam tratados antes da publicação do edital. Permitir que o Chefe de Compras ignore riscos críticos expõe a FACAPE a responsabilização. A validação jurídica é uma salvaguarda necessária, não opcional. O fluxo adicional compensa pela segurança jurídica que oferece.

---

## ADR-04 — O mapa aprovado deve ser imutável ou editável?

**Data:** 2026-05
**Status:** Aprovado

### Contexto

Após a aprovação do Mapa de Riscos, pode ser necessário fazer ajustes (ex.: objeto do processo mudou, novo risco identificado). O grupo precisava decidir se o mapa aprovado pode ser editado diretamente ou se qualquer mudança gera uma nova versão.

### Alternativas consideradas

| Alternativa | Comportamento | Prós | Contras |
|-------------|--------------|------|---------|
| Edição direta | Mapa aprovado pode ser alterado | Mais simples de implementar | Perde o histórico — não é possível saber o que foi aprovado originalmente |
| **Versionamento imutável** | **Mapa aprovado é bloqueado — alterações criam nova versão** | Rastreabilidade completa, auditável | Requer lógica de versionamento |

### Decisão

**O mapa aprovado é imutável (RN-MR-07).** Qualquer alteração necessária deve criar uma nova versão do mapa, preservando a anterior como histórico arquivado.

### Justificativa

O Mapa de Riscos aprovado é parte do processo oficial enviado ao módulo de Edital (G06) e futuramente à Prefeitura. Alterar um documento já aprovado sem registro formal viola o princípio de auditabilidade exigido pela Lei 14.133/2021. O versionamento garante que sempre seja possível responder: "qual era o mapa de riscos no momento da aprovação?"
