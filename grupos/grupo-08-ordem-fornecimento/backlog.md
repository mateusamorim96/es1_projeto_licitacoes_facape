# Backlog — Grupo 08
## Módulo: Controle de Ordem de Fornecimento (OF)

**Integrantes:** Vitor Barros, Filipe Silva, Michel Batista, João Pedro Carvalho, Kelvin Keite  
**Disciplina:** Engenharia de Software I — 2026.1  
**Professor:** Mateus Silva

---

## Épicos

| ID | Épico | Descrição |
|----|-------|-----------|
| EP-01 | Geração de OF | Gerar a Ordem de Fornecimento após resultado do pregão |
| EP-02 | Regras de Conformidade | Validar cota ME/EPP, fracionamento e limites de adesão |
| EP-03 | Cotação | Registrar e validar cotação de preços com fornecedores |
| EP-04 | Acompanhamento | Rastrear envio e entrega da OF |
| EP-05 | Auditoria | Emitir eventos de rastreabilidade para G09 |

---

## User Stories

### EP-01 — Geração de OF

**US-01**
> Como **Chefe de Compras**, quero **receber automaticamente o resultado do pregão do módulo G07**, para que eu possa **iniciar a emissão da OF sem retrabalho manual**.

- **Critérios de aceite:**
  - O sistema recebe os dados do pregão (empresa vencedora, CNPJ, itens, preços) via integração com G07.
  - Dados incompletos ou inválidos são rejeitados com mensagem de erro descritiva.
  - Um rascunho de OF é criado automaticamente com status `AGUARDANDO_EMISSÃO`.
- **Prioridade:** Alta | **Pontos:** 5

---

**US-02**
> Como **Chefe de Compras**, quero **gerar uma Ordem de Fornecimento com número sequencial, itens e prazo de entrega**, para que o fornecedor saiba exatamente o que deve entregar e quando.

- **Critérios de aceite:**
  - OF gerada contém: número sequencial (ex.: OF-2026-0042), fornecedor, CNPJ, itens, quantidades, valores unitários e total, prazo de entrega, local de entrega.
  - OF só é gerada se a ata SRP tiver saldo suficiente e validade vigente.
  - Saldo da ata é deduzido automaticamente após emissão.
- **Prioridade:** Alta | **Pontos:** 8

---

**US-03**
> Como **Chefe de Compras**, quero **enviar a OF ao fornecedor com assinatura digital em PDF**, para que o envio seja rastreável e tenha validade legal.

- **Critérios de aceite:**
  - PDF da OF é gerado com todos os campos e assinatura do ordenador de despesas.
  - Data e hora do envio são registradas.
  - Status da OF muda para `ENVIADA` após envio bem-sucedido.
  - Em caso de falha no envio, status permanece `EMITIDA` e erro é registrado.
- **Prioridade:** Alta | **Pontos:** 5

---

### EP-02 — Regras de Conformidade

**US-04**
> Como **Chefe de Licitações**, quero que o sistema **aplique automaticamente as regras de cota ME/EPP por item**, para garantir conformidade com a Lei Complementar 123/2006 sem cálculo manual.

- **Critérios de aceite:**
  - Itens com valor ≤ R$ 80.000 são marcados como exclusivos ME/EPP (RN-01).
  - Itens com valor > R$ 80.000 têm 25% do quantitativo reservado para ME/EPP, arredondado para baixo (RN-02).
  - Valor exatamente R$ 80.000 é tratado como exclusivo ME/EPP.
  - Fundamento normativo é registrado para auditoria.
- **Prioridade:** Alta | **Pontos:** 5

---

**US-05**
> Como **Chefe de Compras**, quero que o sistema **detecte e impeça o fracionamento de despesa**, para evitar irregularidades e possíveis sanções administrativas.

- **Critérios de aceite:**
  - Se já existir DFD aberto para a mesma categoria no exercício corrente, sistema alerta e sugere mesclagem (RN-03).
  - Se já existir processo homologado para a mesma categoria no mesmo exercício, sistema bloqueia criação de novo processo.
  - Mensagem de bloqueio exibe o número do processo existente para referência.
- **Prioridade:** Alta | **Pontos:** 5

---

**US-06**
> Como **Gestor de Contratos**, quero que o sistema **controle o limite de adesão a atas alheias (carona)**, para não ultrapassar 50% do quantitativo original da ata (RN-08).

- **Critérios de aceite:**
  - Sistema consulta o total de adesões já realizadas à ata de outro órgão.
  - Adesão de até 50% do quantitativo original é permitida.
  - Adesão acima de 50% é bloqueada com mensagem indicando o limite disponível.
- **Prioridade:** Média | **Pontos:** 3

---

### EP-03 — Cotação

**US-07**
> Como **Chefe de Licitações**, quero que o sistema **valide que há pelo menos 3 fornecedores distintos na cotação e calcule a média automaticamente**, para garantir competitividade e conformidade com RN-10.

- **Critérios de aceite:**
  - O sistema exige CNPJs distintos para contar como fornecedores diferentes.
  - Com menos de 3 fornecedores, o sistema bloqueia a finalização e informa quantos faltam.
  - Com 3 ou mais fornecedores, calcula e sugere a média aritmética como valor de referência.
- **Prioridade:** Alta | **Pontos:** 3

---

### EP-04 — Acompanhamento

**US-08**
> Como **Chefe de Compras**, quero **acompanhar o status de entrega da OF em um painel**, para ter visibilidade em tempo real sobre o cumprimento do prazo pelo fornecedor.

- **Critérios de aceite:**
  - Painel exibe todas as OFs emitidas com status atual (EMITIDA / ENVIADA / RECEBIDA / ENTREGUE).
  - OFs com prazo vencido sem entrega confirmada são destacadas em vermelho.
  - Chefe de Compras pode registrar o recebimento da entrega diretamente no painel.
- **Prioridade:** Média | **Pontos:** 5

---

**US-09**
> Como **Chefe de Compras**, quero receber **alertas automáticos de vencimento de ata SRP (30 dias antes)**, para ter tempo hábil de emitir as OFs necessárias antes do vencimento.

- **Critérios de aceite:**
  - Sistema identifica atas com validade dentro de 30 dias que ainda possuem saldo.
  - Alerta é exibido no painel do módulo e enviado por e-mail ao Chefe de Compras.
  - Alerta inclui: número da ata, data de vencimento, saldo disponível e itens.
- **Prioridade:** Média | **Pontos:** 3

---

### EP-05 — Auditoria

**US-10**
> Como **Administrador do sistema**, quero que o módulo G08 **registre eventos de auditoria imutáveis para o G09**, para garantir rastreabilidade completa de quem gerou, enviou e recebeu cada OF.

- **Critérios de aceite:**
  - Eventos registrados: recebimento do resultado do pregão, geração da OF, envio ao fornecedor, confirmação de recebimento, registro de entrega.
  - Cada evento contém: timestamp, usuário responsável, dados da OF, fundamento normativo quando aplicável.
  - Eventos são enviados ao G09 em tempo real via integração definida no contrato de interface.
- **Prioridade:** Alta | **Pontos:** 5

---

## Resumo do Backlog

| ID | User Story | Épico | Prioridade | Pontos |
|----|-----------|-------|-----------|--------|
| US-01 | Receber resultado do pregão de G07 | EP-01 | Alta | 5 |
| US-02 | Gerar OF com número sequencial | EP-01 | Alta | 8 |
| US-03 | Enviar OF com assinatura digital | EP-01 | Alta | 5 |
| US-04 | Aplicar regras de cota ME/EPP | EP-02 | Alta | 5 |
| US-05 | Detectar fracionamento de despesa | EP-02 | Alta | 5 |
| US-06 | Controlar limite de adesão carona | EP-02 | Média | 3 |
| US-07 | Validar cotação com mínimo 3 fornecedores | EP-03 | Alta | 3 |
| US-08 | Painel de acompanhamento de entrega | EP-04 | Média | 5 |
| US-09 | Alertas de vencimento de ata | EP-04 | Média | 3 |
| US-10 | Registrar eventos de auditoria para G09 | EP-05 | Alta | 5 |

**Total de pontos:** 47
