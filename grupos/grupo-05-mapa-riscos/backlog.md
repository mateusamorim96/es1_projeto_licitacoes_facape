# Backlog — Histórias de Usuário


## US-01 — Identificar risco do produto

**Como** Chefe de Licitações,
**quero** cadastrar um risco informando descrição, causa, consequência e categoria,
**para que** os riscos do processo fiquem formalmente registrados antes da geração do mapa.

**Critérios de aceitação:**
- [ ] O sistema exige preenchimento de: descrição, causa, consequência e categoria
- [ ] A categoria deve ser uma das opções: Legal/Regulatório, Técnico/Qualidade, Mercado/Preço, Operacional/Prazo, Orçamentário/Financeiro
- [ ] O sistema registra data, hora e usuário que cadastrou o risco
- [ ] O risco fica vinculado ao processo licitatório correspondente

---

## US-02 — Avaliar probabilidade e impacto

**Como** Chefe de Licitações,
**quero** informar a probabilidade (1 a 5) e o impacto (1 a 5) de cada risco,
**para que** o sistema calcule automaticamente o score e classifique o nível de criticidade.

**Critérios de aceitação:**
- [ ] O sistema aceita apenas valores inteiros de 1 a 5 para probabilidade e impacto
- [ ] O score é calculado automaticamente: `score = probabilidade × impacto`
- [ ] O nível é classificado: 1–4 = Baixo · 5–9 = Médio · 10–14 = Alto · 15–25 = Crítico
- [ ] O sistema exibe o nível com cor correspondente (verde, amarelo, laranja, vermelho)

---

## US-03 — Definir mitigação para um risco

**Como** Chefe de Licitações,
**quero** cadastrar ao menos uma medida de mitigação para cada risco identificado,
**para que** o processo tenha ações planejadas de prevenção ou contingência documentadas.

**Critérios de aceitação:**
- [ ] O tipo da medida deve ser: Preventiva, Contingencial, Por Transferência ou Por Aceitação
- [ ] A medida exige: descrição e prazo de implementação
- [ ] É possível cadastrar múltiplas medidas para o mesmo risco
- [ ] O sistema impede a geração do mapa se algum risco não tiver mitigação (RN-MR-04)

---

## US-04 — Gerar Mapa de Riscos

**Como** Chefe de Licitações,
**quero** gerar o Mapa de Riscos após identificar e avaliar todos os riscos do produto,
**para que** o documento fique disponível para aprovação e posterior envio ao módulo de Edital (G06).

**Critérios de aceitação:**
- [ ] O sistema valida que todos os riscos possuem mitigação antes de gerar
- [ ] Se houver risco sem mitigação, o sistema exibe a lista de pendências e bloqueia a geração
- [ ] O mapa gerado consolida todos os riscos com score, nível e mitigações
- [ ] O status do mapa muda para AGUARDANDO_APROVACAO após a geração

---

## US-05 — Aprovar Mapa de Riscos

**Como** Chefe de Compras,
**quero** revisar e aprovar o Mapa de Riscos submetido pelo Chefe de Licitações,
**para que** o processo possa avançar para a elaboração do Edital (G06).

**Critérios de aceitação:**
- [ ] Se houver risco com score ≥ 15, o Jurídico Interno deve validar antes da aprovação (RN-MR-03)
- [ ] Ao aprovar, o sistema registra: aprovador, data e hora
- [ ] O mapa aprovado é imutável — alterações geram nova versão (RN-MR-07)
- [ ] O mapa aprovado é disponibilizado automaticamente para o G06

---

## US-06 — Validar riscos críticos (Jurídico Interno)

**Como** Jurídico Interno,
**quero** analisar e registrar parecer sobre os riscos com score ≥ 15,
**para que** os riscos críticos tenham embasamento legal antes da aprovação final do mapa.

**Critérios de aceitação:**
- [ ] O Jurídico recebe notificação quando o mapa contém riscos com score ≥ 15
- [ ] A tela exibe apenas os riscos críticos com suas mitigações
- [ ] É possível registrar parecer favorável ou desfavorável com justificativa
- [ ] Parecer desfavorável devolve o mapa para revisão com o motivo registrado

---

## US-07 — Rejeitar mapa e solicitar revisão

**Como** Chefe de Compras,
**quero** rejeitar o mapa e registrar o motivo,
**para que** o Chefe de Licitações saiba exatamente o que precisa ser corrigido.

**Critérios de aceitação:**
- [ ] O campo de motivo da rejeição é obrigatório
- [ ] O sistema notifica automaticamente o Chefe de Licitações com o motivo
- [ ] O mapa volta ao status EM_ELABORACAO para edição
- [ ] O histórico de rejeições fica registrado no sistema

---

## Resumo do Backlog

| ID | História | Ator | Prioridade |
|----|----------|------|-----------|
| US-01 | Identificar risco do produto | Chefe de Licitações | Alta |
| US-02 | Avaliar probabilidade e impacto | Chefe de Licitações | Alta |
| US-03 | Definir mitigação para um risco | Chefe de Licitações | Alta |
| US-04 | Gerar Mapa de Riscos | Chefe de Licitações | Alta |
| US-05 | Aprovar Mapa de Riscos | Chefe de Compras | Alta |
| US-06 | Validar riscos críticos | Jurídico Interno | Média |
| US-07 | Rejeitar mapa e solicitar revisão | Chefe de Compras | Média |
