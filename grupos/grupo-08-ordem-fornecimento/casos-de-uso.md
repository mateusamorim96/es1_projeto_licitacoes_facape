# Casos de Uso — Grupo 08
## Módulo: Controle de Ordem de Fornecimento (OF)

**Integrantes:** Vitor Barros Batista (26341), Filipe Silva Cavalcanti (26211), Michel Batista (26075), João Pedro Ribeiro (26640), Kelvin Keite  
**Disciplina:** Engenharia de Software I — 2026.1  
**Professor:** Mateus Silva

---

## Atores

| Ator | Descrição |
|------|-----------|
| **Chefe de Compras** | Responsável por emitir e acompanhar Ordens de Fornecimento |
| **Chefe de Licitações** | Valida regras de negócio e autoriza emissão da OF |
| **Fornecedor** | Recebe a OF e confirma recebimento/entrega |
| **G07 (Sistema)** | Módulo externo que fornece o resultado do pregão |
| **G09 (Sistema)** | Módulo de auditoria que recebe eventos do módulo G08 |

---

## UC-01 — Receber Resultado do Pregão

| Campo | Valor |
|-------|-------|
| **ID** | UC-01 |
| **Nome** | Receber Resultado do Pregão |
| **Ator Principal** | G07 (Sistema de Acompanhamento Externo) |
| **Pré-condição** | Pregão foi concluído e homologado pela Prefeitura |
| **Pós-condição** | Resultado do pregão registrado no módulo G08 |

**Fluxo Principal:**
1. G07 envia para G08 os dados do resultado do pregão (empresa vencedora, itens, preços unitários, CNPJ).
2. O sistema valida os dados recebidos (campos obrigatórios, CNPJ válido, preços positivos).
3. O sistema registra o resultado e cria um rascunho de OF com status `AGUARDANDO_EMISSÃO`.
4. O sistema emite evento de auditoria para G09: "Resultado do pregão recebido".

**Fluxos Alternativos:**
- **FA-01:** Dados incompletos ou inválidos → sistema rejeita e notifica G07 para reenvio.

---

## UC-02 — Gerar Ordem de Fornecimento

| Campo | Valor |
|-------|-------|
| **ID** | UC-02 |
| **Nome** | Gerar Ordem de Fornecimento |
| **Ator Principal** | Chefe de Compras |
| **Pré-condição** | Resultado do pregão registrado (UC-01 concluído); ata SRP vigente com saldo disponível |
| **Pós-condição** | OF gerada com número sequencial e status `EMITIDA` |

**Fluxo Principal:**
1. Chefe de Compras acessa o módulo e seleciona o resultado do pregão.
2. O sistema exibe rascunho da OF com itens, quantidades, valores unitários, fornecedor, prazo de entrega e local.
3. Chefe de Compras revisa e confirma os dados.
4. O sistema valida: saldo da ata SRP suficiente (RN-04), ata dentro da validade (RN-04), sem fracionamento (RN-03).
5. Sistema gera número sequencial da OF (ex.: OF-2026-0042).
6. Sistema deduz quantitativo do saldo da ata SRP.
7. Sistema registra OF com status `EMITIDA` e emite evento para G09.

**Fluxos Alternativos:**
- **FA-01:** Saldo insuficiente → sistema bloqueia e exibe mensagem de erro.
- **FA-02:** Ata vencida → sistema bloqueia e exibe data de vencimento.
- **FA-03:** Fracionamento detectado → sistema alerta e redireciona para consolidação.

---

## UC-03 — Validar Regras de Cota ME/EPP

| Campo | Valor |
|-------|-------|
| **ID** | UC-03 |
| **Nome** | Validar Regras de Cota ME/EPP |
| **Ator Principal** | Chefe de Licitações |
| **Pré-condição** | Item sendo incluído na OF com valor estimado informado |
| **Pós-condição** | Item classificado como exclusivo ME/EPP ou com cota reservada de 25% |

**Fluxo Principal:**
1. Chefe de Licitações informa o valor total estimado do item.
2. Sistema avalia: se valor ≤ R$ 80.000 → classifica como exclusivo ME/EPP (RN-01).
3. Se valor > R$ 80.000 → calcula 25% do quantitativo para cota ME/EPP, arredondando para baixo (RN-02).
4. Sistema registra a classificação no item e exibe ao usuário.
5. Sistema emite evento de auditoria para G09 com fundamento normativo (Lei Complementar 123/2006).

**Fluxos Alternativos:**
- **FA-01:** Valor exatamente igual a R$ 80.000 → sistema aplica RN-01 (exclusivo ME/EPP) e registra como caso de borda.

---

## UC-04 — Enviar OF ao Fornecedor

| Campo | Valor |
|-------|-------|
| **ID** | UC-04 |
| **Nome** | Enviar OF ao Fornecedor |
| **Ator Principal** | Chefe de Compras |
| **Pré-condição** | OF gerada com status `EMITIDA` (UC-02 concluído) |
| **Pós-condição** | OF enviada com status `ENVIADA`; fornecedor notificado |

**Fluxo Principal:**
1. Chefe de Compras seleciona a OF emitida e aciona "Enviar ao Fornecedor".
2. Sistema gera PDF da OF com assinatura digital do ordenador de despesas.
3. Sistema registra data e hora do envio.
4. Sistema altera status da OF para `ENVIADA`.
5. Sistema notifica o Fornecedor (e-mail com PDF anexado).
6. Sistema emite evento de auditoria para G09: "OF enviada ao fornecedor".

**Fluxos Alternativos:**
- **FA-01:** Falha no envio de e-mail → sistema registra erro e mantém status `EMITIDA` para nova tentativa.

---

## UC-05 — Acompanhar Status de Entrega

| Campo | Valor |
|-------|-------|
| **ID** | UC-05 |
| **Nome** | Acompanhar Status de Entrega |
| **Ator Principal** | Chefe de Compras |
| **Pré-condição** | OF com status `ENVIADA` |
| **Pós-condição** | Status da OF atualizado conforme confirmação do fornecedor |

**Fluxo Principal:**
1. Fornecedor confirma recebimento da OF → sistema atualiza status para `RECEBIDA_PELO_FORNECEDOR`.
2. Chefe de Compras acompanha o prazo de entrega no painel do módulo.
3. Fornecedor realiza a entrega → Chefe de Compras registra o recebimento no sistema.
4. Sistema atualiza status para `ENTREGUE` e registra data de recebimento.
5. Sistema emite evento para G09: "Entrega confirmada".

**Fluxos Alternativos:**
- **FA-01:** Prazo de entrega vencido sem confirmação → sistema emite alerta automático ao Chefe de Compras.
- **FA-02:** Entrega parcial → sistema registra quantidade entregue e mantém OF em aberto para complementação.

---

## UC-06 — Controlar Adesão a Ata de Outro Órgão (Carona)

| Campo | Valor |
|-------|-------|
| **ID** | UC-06 |
| **Nome** | Controlar Adesão a Ata de Outro Órgão |
| **Ator Principal** | Chefe de Compras |
| **Pré-condição** | Ata de outro órgão identificada para adesão |
| **Pós-condição** | Adesão registrada dentro do limite de 50% ou bloqueada |

**Fluxo Principal:**
1. Chefe de Compras informa quantidade desejada para adesão à ata alheia.
2. Sistema consulta quantitativo original da ata e total de adesões já realizadas.
3. Sistema verifica se (adesões existentes + nova adesão) ≤ 50% do quantitativo original (RN-08).
4. Se dentro do limite → registra adesão e emite evento para G09.
5. Se acima do limite → bloqueia e exibe mensagem com o limite disponível.

---

## UC-07 — Validar Cotação com Mínimo de 3 Fornecedores

| Campo | Valor |
|-------|-------|
| **ID** | UC-07 |
| **Nome** | Validar Cotação com Mínimo de 3 Fornecedores |
| **Ator Principal** | Chefe de Licitações |
| **Pré-condição** | Processo de cotação iniciado |
| **Pós-condição** | Cotação validada com mínimo de 3 fornecedores distintos e média calculada |

**Fluxo Principal:**
1. Chefe de Licitações registra propostas dos fornecedores consultados.
2. Sistema valida que há ao menos 3 fornecedores com CNPJ distintos (RN-10).
3. Sistema calcula a média aritmética dos preços informados.
4. Sistema sugere o valor de referência com base na média calculada.
5. Sistema emite evento para G09 com os dados da cotação.

**Fluxos Alternativos:**
- **FA-01:** Menos de 3 fornecedores → sistema bloqueia a finalização e exibe quantos ainda faltam.
