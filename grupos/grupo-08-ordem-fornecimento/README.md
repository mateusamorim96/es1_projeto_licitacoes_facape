# Grupo 08 — Controle de Ordem de Fornecimento (OF)

## Módulo do Sistema

Geração da Ordem de Fornecimento após resultado do pregão e acompanhamento da entrega pelo fornecedor.

## Responsabilidade

- Receber resultado do pregão (G07): empresa vencedora, valor final
- Gerar OF com número, itens, preços, local de entrega, prazo e condições de pagamento
- Enviar OF ao fornecedor (assinada digitalmente)
- Acompanhar confirmação de recebimento da OF
- Acompanhar status de entrega

**Entradas:** Resultado do pregão (G07)  
**Saídas:** OF gerada e enviada ao fornecedor

---

## Entregas Mínimas

| Artefato | Descrição |
|----------|-----------|
| Casos de uso (mínimo 4) | Gerar OF, validar, enviar ao fornecedor, acompanhar entrega |
| Diagrama UML de classes | `OrdemFornecimento`, `ItemOF`, `Fornecedor`, `Entrega`, `Pagamento` |
| Diagrama de sequência | Geração → envio → acompanhamento |
| BPMN | Ciclo completo de OF |
| Backlog | Mínimo 5 histórias de usuário |
| ADRs (mínimo 2) | Ex.: assinatura digital? Qual padrão? |
| Testes | Cálculo de totais, validação de prazos |
| Auditoria | Quem gerou OF, quando foi enviada, confirmação de recebimento |

---

## Interfaces com Outros Módulos

- **Entrada ← G07 (Acompanhamento Externo):** Resultado do pregão
- **Saída → G09 (Auditoria):** Eventos de geração e envio da OF

---

## Entrega do Grupo

> Preencha esta seção ao finalizar:

- **Integrantes:** Vitor Barros, Filipe Silva, Michel Batista, João Pedro Carvalho, Kelvin Keite
- **Data de entrega:** 2026-06-03
- **Branch/PR:** https://github.com/mateusamorim96/es1_projeto_licitacoes_facape

---

## 📋 O que entregar

### Artefato 1: `plano-de-testes.md`
Documento descrevendo a estratégia geral de testes:
- Objetivos e escopo
- Pirâmide de testes adotada
- Critérios de entrada e saída
- Ambientes de teste
- Riscos de qualidade identificados

### Artefato 2: `casos-de-teste/CT-XX.md` (mínimo 12 casos)
Casos de teste individuais, organizados por funcionalidade.

### Artefato 3: `matriz-rastreabilidade.md`
Matriz que vincula Casos de Teste ↔ User Stories ↔ Regras de Negócio.

---

## 🔍 Funcionalidades Prioritárias para Teste

Com base nos gargalos identificados no contexto do sistema, priorize:

| Prioridade | Funcionalidade | Regra de Negócio |
|-----------|---------------|-----------------|
| 🔴 Alta | Verificação de fracionamento de despesa | RN-03 |
| 🔴 Alta | Validação de cota ME/EPP por item | RN-01, RN-02 |
| 🔴 Alta | Emissão de OF com verificação de saldo da ata | RN-04 |
| 🔴 Alta | Cotação com mínimo de 3 fornecedores | RN-10 |
| 🟡 Média | Alerta de vencimento de ata (30 dias antes) | RN-04 |
| 🟡 Média | Bloqueio de adesão acima de 50% do quantitativo | RN-08 |
| 🟡 Média | Prazo mínimo de entrega por categoria | - |
| 🟢 Baixa | Notificação de prazo de entrega expirado | - |

---

## 📄 Template de Caso de Teste

```markdown
# CT-XX — [Nome do Caso de Teste]

## Identificação

| Campo | Valor |
|-------|-------|
| **ID** | CT-XX |
| **User Story** | US-XX |
| **Regra de Negócio** | RN-XX |
| **Prioridade** | Alta / Média / Baixa |
| **Tipo de Teste** | Funcional / Borda / Negativo / Integração |
| **Nível** | Unitário / Integração / Sistema / Aceite |

## Pré-condições

- (estado do sistema antes do teste)

## Dados de Entrada

| Campo | Valor |
|-------|-------|
| | |

## Passos de Execução

1. ...
2. ...
3. ...

## Resultado Esperado

> (descreva o comportamento esperado do sistema)

## Resultado Obtido

> (preencher durante a execução)

## Status

[ ] Não executado | [ ] Passou | [ ] Falhou | [ ] Bloqueado
```

---

## 📋 Casos de Teste Obrigatórios

### Bloco 1: Regras de Cota ME/EPP

**CT-01**: Item com valor total = R$ 79.999 → deve ser exclusivo ME/EPP  
**CT-02**: Item com valor total = R$ 80.001 → deve ter cota exclusiva de 25%  
**CT-03**: Item com valor total = R$ 80.000 → caso de borda — qual regra se aplica?  
**CT-04**: Cota ME/EPP calculada como 25% do valor → arredondamento para menos  

### Bloco 2: Fracionamento de Despesa

**CT-05**: Criar DFD de Expediente quando já existe outro aberto → sistema deve alertar e mesclar  
**CT-06**: Criar dois processos de expediente no mesmo exercício → sistema deve bloquear  

### Bloco 3: Ata SRP e Ordens de Fornecimento

**CT-07**: Emitir OF de 100 unidades com saldo de ata = 100 → deve permitir  
**CT-08**: Emitir OF de 101 unidades com saldo de ata = 100 → deve bloquear  
**CT-09**: Emitir OF em ata com validade vencida → deve bloquear com mensagem  
**CT-10**: Adesão de 50% do quantitativo da ata → deve permitir  
**CT-11**: Adesão de 51% do quantitativo da ata → deve bloquear (RN-08)  

### Bloco 4: Cotação

**CT-12**: Iniciar cotação com 2 fornecedores → deve exigir mínimo de 3  
**CT-13**: Cotação com 3 fornecedores → deve calcular e sugerir média corretamente  

---

## 🛠️ Ferramentas Recomendadas

| Ferramenta | Link | Observação |
|-----------|------|------------|
| **Markdown** | qualquer editor | Formato dos casos de teste |
| **TestLink** | [testlink.org](https://testlink.org) | Gestão de casos de teste open-source |
| **Zephyr Scale** | Integrado ao Jira | Para times que usam Jira |
| **Excel/Google Sheets** | - | Alternativa simples para matriz de rastreabilidade |

---

## 📁 Estrutura esperada da pasta

```
grupo-08-testes/
├── README.md
├── plano-de-testes.md
├── matriz-rastreabilidade.md
└── casos-de-teste/
    ├── CT-01-cota-mepp-abaixo-80k.md
    ├── CT-02-cota-mepp-acima-80k.md
    ├── CT-03-cota-mepp-borda-80k.md
    ├── CT-04-arredondamento-cota.md
    ├── CT-05-fracionamento-alerta.md
    ├── CT-06-fracionamento-bloqueio.md
    ├── CT-07-of-saldo-exato.md
    ├── CT-08-of-saldo-insuficiente.md
    ├── CT-09-of-ata-vencida.md
    ├── CT-10-adesao-50pct.md
    ├── CT-11-adesao-51pct.md
    ├── CT-12-cotacao-2-fornecedores.md
    └── CT-13-cotacao-media.md
```

---

## ✏️ Seção de Entrega (preencher pelo grupo)

**Integrantes:**
- Vitor Barros
- Filipe Silva
- Michel Batista
- João Pedro Carvalho
- Kelvin Keite

**Decisões tomadas:**
> - Validação do saldo da ata SRP ocorre no momento da confirmação da emissão da OF (commit atômico), não no rascunho — garante consistência sem mecanismo de reserva prévia (ver ADR-01).
> - Identificador da OF segue o formato `OF-{ANO}-{SEQUENCIAL}` (ex.: OF-2026-0042), compatível com a nomenclatura manual já usada na FACAPE (ver ADR-02).
> - Valor exato de R$ 80.000 é tratado como exclusivo ME/EPP (RN-01 aplica operador `<=`).
> - Testes documentados em Markdown com resultados obtidos preenchidos; sem implementação de código executável neste escopo acadêmico.

**Limitações identificadas:**
> - Contrato de integração com G07 e G09 foi definido unilateralmente pelo grupo; não houve validação formal com os grupos G07 e G09 durante o desenvolvimento.
> - Em ambiente de produção com alta concorrência, a validação atômica de saldo exigiria controle transacional (lock otimista ou pessimista) para evitar condição de corrida.
> - Assinatura digital da OF foi modelada como metadado; integração real com ICP-Brasil ou Gov.br está fora do escopo acadêmico.
