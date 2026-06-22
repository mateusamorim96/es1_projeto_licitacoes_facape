# Casos de Uso — Módulo: Mapa de Riscos

## Sobre include e extend

Alguns casos de uso **chamam outros automaticamente**:

- **«include»** — sempre acontece, é obrigatório. Exemplo: ao classificar probabilidade e impacto (UC-02), o sistema **sempre** calcula o score (UC-14).
- **«extend»** — só acontece se uma condição for verdadeira. Exemplo: ao aprovar o mapa (UC-05), a validação jurídica (UC-11) **só** é acionada se houver risco com score ≥ 15.

---

## Atores

| Ator | Papel |
|------|-------|
| **Chefe de Licitações** | Cadastra e acompanha os riscos no dia a dia |
| **Chefe de Compras** | Aprova o mapa e atribui responsáveis |
| **Jurídico Interno** | Valida riscos críticos (score ≥ 15) antes da aprovação final |
| **Sistema** | Executa tarefas automáticas sem interação do usuário |

---

## UC-01 — Cadastrar Risco

**Ator:** Chefe de Licitações
**Pré-condição:** Usuário autenticado e mapa com status EM_ELABORACAO.

1. Chefe de Licitações acessa o processo licitatório.
2. Sistema exibe o formulário de cadastro de risco.
3. Chefe de Licitações preenche: descrição, causa, consequência e categoria (Legal/Regulatório, Técnico/Qualidade, Mercado/Preço, Operacional/Prazo ou Orçamentário/Financeiro).
4. Sistema valida os campos obrigatórios e associa a categoria ao risco.
5. Sistema salva o risco e exibe confirmação.

**Pós-condição:** Risco cadastrado, categorizado e vinculado ao mapa do processo.

---

## UC-02 — Classificar Probabilidade e Impacto

**Ator:** Chefe de Licitações
**Pré-condição:** Risco cadastrado (UC-01 executado).

1. Chefe de Licitações informa a probabilidade do risco (valor de 1 a 5).
2. Chefe de Licitações informa o impacto do risco (valor de 1 a 5).
3. Sistema executa UC-14 (Calcular Score) automaticamente.
4. Sistema exibe o score e o nível correspondente (Baixo, Médio, Alto ou Crítico).

**Pós-condição:** Risco classificado com score e nível definidos.

> **«include» UC-14** — o cálculo do score sempre acontece ao classificar.

---

## UC-03 — Definir Medida de Resposta

**Ator:** Chefe de Licitações
**Pré-condição:** Risco cadastrado e classificado.

1. Chefe de Licitações seleciona o risco que receberá a medida.
2. Sistema exibe o formulário de medida de resposta.
3. Chefe de Licitações informa: tipo (Preventiva, Contingencial, Transferência ou Aceitação), descrição e prazo.
4. Sistema valida os campos e salva a medida vinculada ao risco.

**Pós-condição:** Risco possui ao menos uma medida de resposta (RN-MR-04).

---

## UC-04 — Gerar Mapa de Riscos

**Ator:** Chefe de Licitações
**Pré-condição:** Todos os riscos do processo cadastrados, classificados e com medidas definidas.

1. Chefe de Licitações solicita a geração do mapa.
2. Sistema verifica se todos os riscos possuem medidas de resposta (RN-MR-04).
3. Se houver risco sem medida: sistema bloqueia e exibe lista de pendências.
4. Se mapa completo: sistema consolida todos os riscos, scores, níveis e medidas.
5. Sistema gera o Mapa de Riscos e muda o status para AGUARDANDO_APROVACAO.
6. Sistema notifica o Chefe de Compras que o mapa aguarda aprovação.

**Pós-condição:** Mapa de Riscos gerado e submetido para aprovação.

**Fluxo alternativo — FA01 (risco sem medida):**
No passo 3, se algum risco não tiver medida vinculada, o sistema exibe a mensagem: *"Impossível gerar mapa. O risco [código] não possui medida de resposta cadastrada."* O caso de uso encerra sem gerar o mapa.

---

## UC-05 — Aprovar Mapa de Riscos

**Ator:** Chefe de Compras
**Pré-condição:** Mapa com status AGUARDANDO_APROVACAO.

1. Chefe de Compras acessa o mapa submetido para revisão.
2. Sistema verifica se existe algum risco com score ≥ 15.
   - Se sim: executa UC-11 (Validar Riscos Legais) antes de prosseguir.
3. Chefe de Compras revisa o mapa completo.
4. Chefe de Compras confirma a aprovação.
5. Sistema cria versão imutável do mapa (RN-MR-07) e muda status para APROVADO.
6. Sistema disponibiliza o mapa para o módulo G06 (Edital).

**Pós-condição:** Mapa aprovado, versionado e disponível para elaboração do edital.

> **«extend» UC-11** — só acionado se houver risco com score ≥ 15 (RN-MR-03).

**Fluxo alternativo — FA01 (rejeição):**
No passo 4, o Chefe de Compras pode rejeitar o mapa. Nesse caso, registra o motivo, o sistema muda o status para REVISAO_SOLICITADA e notifica o Chefe de Licitações com as pendências descritas.

---

## UC-11 — Validar Riscos Legais *(extend de UC-05)*

**Ator:** Jurídico Interno
**Pré-condição:** Mapa contém ao menos um risco com score ≥ 15 (RN-MR-03).

1. Sistema notifica o Jurídico Interno sobre os riscos críticos.
2. Jurídico Interno acessa a tela de análise jurídica.
3. Sistema exibe apenas os riscos com score ≥ 15 e suas medidas.
4. Jurídico Interno analisa cada risco crítico e registra parecer (favorável ou desfavorável com motivo).

**Pós-condição:** Parecer jurídico registrado. Se favorável, mapa prossegue para aprovação final do Chefe de Compras.

**Fluxo alternativo — FA01 (parecer negativo):**
No passo 4, se o Jurídico reprovar, o sistema muda o status para REVISAO_SOLICITADA e notifica o Chefe de Licitações com o motivo do parecer.

---

## UC-14 — Calcular Score de Risco *(automático)*

**Ator:** Sistema
**Pré-condição:** Probabilidade e impacto informados (UC-02 em execução).

1. Sistema recebe os valores de probabilidade (1–5) e impacto (1–5).
2. Sistema calcula: `score = probabilidade × impacto`.
3. Sistema classifica o nível: 1–4 = Baixo · 5–9 = Médio · 10–14 = Alto · 15–25 = Crítico.
4. Sistema retorna o score e o nível para exibição.

**Pós-condição:** Score e nível do risco calculados e armazenados.

---

## Regras de Negócio Relacionadas

| ID | Regra |
|----|-------|
| **RN-MR-03** | Riscos com score ≥ 15 exigem validação do Jurídico Interno antes da aprovação |
| **RN-MR-04** | Todo risco deve ter ao menos uma medida de resposta para o mapa ser gerado |
| **RN-MR-07** | Mapa aprovado é imutável — alterações geram nova versão |
