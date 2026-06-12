# Relatório Técnico Final — Grupo 08
## Módulo: Controle de Ordem de Fornecimento (OF)

**Integrantes:** Vitor Barros, Filipe Silva, Michel Batista, João Pedro Carvalho, Kelvin Keite  
**Disciplina:** Engenharia de Software I — 2026.1  
**Professor:** Mateus Silva  
**Data:** 2026-06-02

---

## 1. Problema e Contexto

O módulo G08 resolve a fase de execução do ciclo de compras da FACAPE: após o pregão eletrônico ser realizado pela Prefeitura de Petrolina e o resultado ser homologado, o setor de compras da FACAPE precisa emitir formalmente uma Ordem de Fornecimento (OF) à empresa vencedora.

Hoje, esse processo é inteiramente manual — OFs são redigidas em papel, sem validação automática de saldo de ata, sem controle de fracionamento de despesa e sem rastreabilidade entre a demanda original e a entrega final. Isso gera retrabalho, risco de irregularidades e ausência de trilha de auditoria.

---

## 2. Fronteiras do Módulo

| Entrada | Origem | Descrição |
|---------|--------|-----------|
| Resultado do pregão | G07 | Empresa vencedora, CNPJ, itens, preços unitários |

| Saída | Destino | Descrição |
|-------|---------|-----------|
| Eventos de auditoria | G09 | Geração, envio, recebimento e entrega da OF |
| OF em PDF | Fornecedor | Documento formal com assinatura digital |

**Fora do escopo do módulo G08:**
- Execução do pregão eletrônico (Prefeitura).
- Módulo financeiro/empenho (contabilidade).
- Gestão de contratos após assinatura.

---

## 3. Modelo de Domínio

O módulo é centrado na entidade `OrdemFornecimento`, que agrega:

- `ItemOF` — itens com regras de cota ME/EPP aplicadas automaticamente.
- `AtaSRP` — controle de saldo disponível e validade (RN-04, RN-08).
- `Fornecedor` — destinatário da OF.
- `Entrega` — rastreamento do cumprimento do prazo.
- `ResultadoPregao` — dados recebidos de G07.
- `EventoAuditoria` — registros enviados ao G09.

O ciclo de vida da OF segue os estados:
```
AGUARDANDO_EMISSAO → EMITIDA → ENVIADA → RECEBIDA_PELO_FORNECEDOR → ENTREGUE
```

---

## 4. Regras de Negócio Implementadas

| Regra | Descrição | Implementação |
|-------|-----------|--------------|
| RN-01 | Itens ≤ R$ 80k: exclusivos ME/EPP | `ItemOF.aplicarRegrasCotaMEPP()` |
| RN-02 | Itens > R$ 80k: cota de 25% para ME/EPP, arredondado para baixo | `ItemOF.aplicarRegrasCotaMEPP()` |
| RN-03 | Fracionamento de despesa proibido | Validação em `OrdemFornecimento.emitir()` |
| RN-04 | Saldo e validade da ata SRP | `AtaSRP.calcularSaldoDisponivel()` e `AtaSRP.estaVigente()` |
| RN-08 | Limite de 50% para adesão a ata alheia (carona) | Verificação com flag `AtaSRP.alheia` |
| RN-10 | Mínimo de 3 fornecedores distintos na cotação | `Cotacao.validarMinimoFornecedores()` |

**Nota sobre numeração:** As regras RN-05, RN-06, RN-07 e RN-09 pertencem a outros módulos do sistema (conforme `docs/contexto-do-sistema.md`) e estão fora do escopo do módulo G08. A numeração não é contínua por design — cada grupo recebe as RNs pertinentes ao seu módulo.

---

## 5. Decisões Arquiteturais

### ADR-01 — Validação de saldo no momento da confirmação
Optamos por validar e deduzir o saldo da ata no momento exato da confirmação da OF (commit atômico). Isso garante consistência sem a complexidade de um mecanismo de reserva prévia. Em produção real com alta concorrência, seria necessário controle transacional com lock otimista ou pessimista.

### ADR-02 — Identificador legível para a OF
Formato `OF-{ANO}-{SEQUENCIAL}` (ex.: OF-2026-0042). Compatível com a nomenclatura já usada manualmente na FACAPE, facilitando a transição e referência cruzada com documentos físicos.

---

## 6. Integração com Outros Módulos

### Entrada: G07 → G08

O módulo G08 recebe do G07 um objeto com os campos:

```json
{
  "numeroProcesso": "string",
  "dataHomologacao": "ISO8601 date",
  "fornecedorVencedor": {
    "cnpj": "string (14 dígitos)",
    "razaoSocial": "string",
    "email": "string"
  },
  "itens": [
    {
      "descricao": "string",
      "quantidade": "integer",
      "valorUnitario": "decimal",
      "unidade": "string"
    }
  ]
}
```

### Saída: G08 → G09

O módulo G08 envia ao G09 eventos com a estrutura:

```json
{
  "tipoEvento": "string (ex: OF_EMITIDA)",
  "timestamp": "ISO8601 datetime",
  "usuarioResponsavel": "string",
  "idEntidade": "string (número da OF)",
  "descricao": "string",
  "fundamentoNormativo": "string (ex: RN-04, Lei 14.133/2021 Art.82)"
}
```

---

## 7. Testes

O módulo possui 13 casos de teste documentados, cobrindo:

| Bloco | CTs | Regras |
|-------|-----|--------|
| Cota ME/EPP | CT-01 a CT-04 | RN-01, RN-02 |
| Fracionamento | CT-05, CT-06 | RN-03 |
| Saldo e validade da ata | CT-07 a CT-09 | RN-04 |
| Limite de adesão carona | CT-10, CT-11 | RN-08 |
| Cotação mínima | CT-12, CT-13 | RN-10 |

A cobertura de todas as regras de negócio críticas foi verificada na `matriz-rastreabilidade.md`.

---

## 8. Trade-offs e Riscos

| Trade-off / Risco | Decisão tomada |
|------------------|----------------|
| Validação de saldo: no rascunho vs. na confirmação | Na confirmação — mais seguro; aceita a limitação de não reservar saldo previamente |
| Assinatura digital: certificado ICP-Brasil vs. assinatura simples | No escopo acadêmico, assinatura representada como metadado; em produção exigiria integração com ICP-Brasil ou Gov.br |
| Integração com G07: síncrona vs. assíncrona | Modelada como síncrona para simplificação acadêmica; em produção, fila de mensagens seria mais resiliente |
| Formato de identificador da OF | Legibilidade humana priorizada sobre unicidade global — suficiente para o porte da FACAPE |

---

## 9. Limitações Identificadas

- Os testes foram especificados em nível de documentação (Markdown); não há implementação de código executável neste escopo.
- O contrato de integração com G07 e G09 foi definido pelo grupo mas não validado formalmente com os grupos G07 e G09 durante o desenvolvimento.
- A regra de caso de borda para RN-01/RN-02 com valor exato de R$ 80.000 foi decidida pelo grupo (aplicar RN-01 — exclusivo ME/EPP), mas a Lei Complementar 123/2006 usa `≤`, o que confirma essa interpretação.

---

## 10. Conclusão

O módulo G08 cobre com completude o ciclo de Ordem de Fornecimento: recebimento do resultado do pregão, geração da OF com validação de regras de negócio, envio ao fornecedor e acompanhamento de entrega. Todos os artefatos obrigatórios foram entregues: casos de uso, UML, diagramas de sequência, BPMN, backlog, ADRs, plano de testes, casos de teste e matriz de rastreabilidade.

A principal contribuição técnica do módulo é a formalização das regras de negócio (RN-01 a RN-10) em lógica verificável, com cobertura total por casos de teste documentados e rastreabilidade até as user stories correspondentes.
