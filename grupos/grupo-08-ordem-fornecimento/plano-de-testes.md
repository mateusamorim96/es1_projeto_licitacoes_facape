# Plano de Testes — Grupo 08
## Módulo: Controle de Ordem de Fornecimento (OF)

**Integrantes:** Vitor Barros, Filipe Silva, Michel Batista, João Pedro Carvalho, Kelvin Keite
**Disciplina:** Engenharia de Software I — 2026.1
**Professor:** Mateus Silva

---

## 1. Objetivos

Este plano define a estratégia de testes do módulo de Controle de Ordem de Fornecimento (OF), responsável por:

- Gerar a OF após resultado do pregão (recebido do G07)
- Validar regras de negócio relacionadas a cotas ME/EPP, saldo de ata, fracionamento de despesa e cotação
- Enviar a OF ao fornecedor e acompanhar a entrega
- Registrar eventos de auditoria para o G09

---

## 2. Escopo

### Dentro do escopo de testes

- Geração da Ordem de Fornecimento com base no resultado do pregão
- Validação das regras de cota ME/EPP (RN-01, RN-02)
- Bloqueio de fracionamento de despesa (RN-03)
- Emissão de OF com verificação de saldo da ata SRP (RN-04)
- Controle de adesão a ata de outro órgão (RN-08)
- Validação de cotação com mínimo de 3 fornecedores (RN-10)
- Acompanhamento de status de entrega
- Geração de eventos de auditoria para G09

### Fora do escopo de testes

- Execução do pregão eletrônico (responsabilidade da Prefeitura)
- Módulo financeiro/empenho (fora do sistema)
- Interface interna dos módulos G01 a G06

---

## 3. Pirâmide de Testes Adotada

```
        ▲
       / \
      /E2E\        <- Poucos: fluxo completo OF gerada -> enviada -> entregue
     /-----\
    /Integr.\      <- Medios: integracao com G07 (entrada) e G09 (saida)
   /---------\
  / Unitarios \    <- Maioria: regras de negocio isoladas (calculos, validacoes)
 /-------------\
```

| Nível | Foco | Quantidade |
|-------|------|------------|
| Unitário | Regras de negócio (cálculos de cota, validação de saldo, arredondamento) | 8 casos |
| Integração | Interface com G07 (resultado do pregão) e G09 (auditoria) | 3 casos |
| Sistema/Aceite | Fluxo ponta a ponta: receber resultado -> gerar OF -> enviar -> acompanhar | 2 casos |

---

## 4. Critérios de Entrada

Os testes só são iniciados quando:

- [ ] O contrato de entrada do módulo (dados recebidos do G07) está definido e mockado
- [ ] As regras de negócio RN-01 a RN-10 estão documentadas e validadas pelo grupo
- [ ] O ambiente de teste está configurado com dados de exemplo (ata SRP, resultado de pregão, fornecedores)

---

## 5. Critérios de Saída (Definition of Done)

Um caso de teste é considerado concluído quando:

- [ ] O resultado obtido corresponde ao resultado esperado descrito no CT
- [ ] Casos negativos (bloqueios) retornam mensagem de erro descritiva
- [ ] Casos de borda foram executados e documentados
- [ ] O status do CT foi preenchido (Passou / Falhou / Bloqueado)

A entrega é considerada concluída quando:

- [ ] Todos os 13 casos obrigatórios foram executados
- [ ] Nenhum caso de prioridade Alta está com status "Falhou" sem justificativa
- [ ] A matriz de rastreabilidade está atualizada

---

## 6. Ambientes de Teste

| Ambiente | Descrição |
|----------|-----------|
| Local | Execução manual dos casos de teste com dados mockados |
| Documentado | Casos descritos em Markdown com resultado esperado e obtido preenchidos |

Não há ambiente de CI/CD automatizado nesta entrega (escopo acadêmico).

---

## 7. Funcionalidades Prioritárias

| Prioridade | Funcionalidade | Regra de Negócio |
|-----------|---------------|-----------------|
| Alta | Verificação de fracionamento de despesa | RN-03 |
| Alta | Validação de cota ME/EPP por item | RN-01, RN-02 |
| Alta | Emissão de OF com verificação de saldo da ata | RN-04 |
| Alta | Cotação com mínimo de 3 fornecedores | RN-10 |
| Média | Alerta de vencimento de ata (30 dias antes) | RN-04 |
| Média | Bloqueio de adesão acima de 50% do quantitativo | RN-08 |
| Média | Prazo mínimo de entrega por categoria | — |
| Baixa | Notificação de prazo de entrega expirado | — |

---

## 8. Riscos de Qualidade

| Risco | Impacto | Mitigação |
|-------|---------|-----------|
| Regras de arredondamento da cota ME/EPP implementadas incorretamente | Alto | Cobrir caso de borda (CT-03, CT-04) |
| Saldo de ata não atualizado em tempo real | Alto | Testar cenário de saldo exato e insuficiente (CT-07, CT-08) |
| Adesão a ata alheia sem validação do limite de 50% | Alto | CT-10 e CT-11 cobrem esse limite |
| Cotação com menos de 3 fornecedores aceita erroneamente | Alto | CT-12 verifica o bloqueio |
| OF emitida em ata com validade vencida | Alto | CT-09 cobre esse cenário |

---

## 9. Índice dos Casos de Teste

| ID | Nome | Prioridade | Regra |
|----|------|-----------|-------|
| CT-01 | Item abaixo de R$ 80k — exclusivo ME/EPP | Alta | RN-01 |
| CT-02 | Item acima de R$ 80k — cota 25% ME/EPP | Alta | RN-02 |
| CT-03 | Item exatamente R$ 80k — caso de borda | Alta | RN-01, RN-02 |
| CT-04 | Arredondamento da cota de 25% | Alta | RN-02 |
| CT-05 | DFD duplicado — sistema alerta e mescla | Alta | RN-03 |
| CT-06 | Dois processos de expediente no mesmo ano | Alta | RN-03 |
| CT-07 | OF com saldo de ata exato — deve permitir | Alta | RN-04 |
| CT-08 | OF com saldo insuficiente — deve bloquear | Alta | RN-04 |
| CT-09 | OF em ata vencida — deve bloquear | Alta | RN-04 |
| CT-10 | Adesão de 50% do quantitativo — deve permitir | Média | RN-08 |
| CT-11 | Adesão de 51% do quantitativo — deve bloquear | Média | RN-08 |
| CT-12 | Cotação com 2 fornecedores — deve exigir mínimo de 3 | Alta | RN-10 |
| CT-13 | Cotação com 3 fornecedores — cálculo da média | Alta | RN-10 |
