# 📑 Diretrizes de Auditoria, Compliance e Observabilidade — Módulo G04 (TR)
*Data: 11 de Junho de 2026* *Responsável: Grupo 04 — Elaboração do Termo de Referência* *Contexto do Projeto: Sistema de Licitações FACAPE (Lei nº 14.133/2021 e LC nº 123/2006)*

---

## 1. Escopo e Objetivos de Governança
De acordo com os requisitos obrigatórios do projeto (**RQ-INT-03** de Compliance e **RQ-INT-05** de Observabilidade), o módulo de Elaboração do Termo de Referência (TR) opera sob o princípio da transparência pública e da segregação de funções.

Este documento formaliza os mecanismos implementados no código do **Grupo 04** para:
1. **Rastreabilidade Ponta a Ponta:** Provar que as especificações do TR derivam exatamente do ETP (G02) e da Ata SRP (G03), sem adulteração humana.
2. **Fundamentação Normativa Automatizada:** Registrar no banco de dados o embasamento legal atrelado aos cálculos de cotas exclusivas/abertas.
3. **Contrato de Auditoria com o G09:** Definir a estrutura dos logs e payloads imutáveis enviados para o barramento do módulo centralizador de compliance.

---

## 2. Trilha de Auditoria Imutável (Rastreabilidade)
Para cada transição de estado no ciclo de vida do Termo de Referência (ex: *Rascunho*, *Consolidado Técnico*, *Aprovado pelo Chefe de Compras*, *Enviado ao G05*), o sistema dispara um evento assíncrono contendo um snapshot estruturado dos dados.

### 2.1 Modelo do Payload Base de Auditoria (JSON)
Este é o contrato de dados que o G04 envia à API/Tópico de auditoria do **Grupo 09**:

```json
{
  "eventId": "evt_tr_20260611_00042",
  "timestamp": "2026-06-11T22:21:00Z",
  "moduloOrigem": "G04_TERMO_DE_REFERENCIA",
  "acao": "CONSOLIDAR_CÁLCULO_COTAS",
  "usuarioResponsavel": {
    "id": "usr_analista_04",
    "perfil": "Analista de Licitação",
    "ipOrigem": "192.168.10.45"
  },
  "entidadesAfetadas": {
    "termoReferenciaId": "TR-2026-0089",
    "etpOrigemId": "ETP-2026-0112",
    "ataSrpOrigemId": "ATA-2026-0034"
  },
  "complianceRules": {
    "leiReferencia": "Lei nº 14.133/2021 (Regras ME/EPP da LC 123/2006)",
    "versaoParametrosId": "V_LEI_14133_REG_MEEPP_2026_01",
    "tetoCotaExclusivaAplicado": 80000.00,
    "percentualCotaExclusivaAplicado": 25.0
  },
  "dadosDoLote": {
    "itemDescricao": "Computadores de Alto Desempenho para Laboratórios",
    "quantidadeTotal": 10,
    "valorUnitarioEstimado": 9000.00,
    "valorTotalLote": 90000.00,
    "cotaExclusivaQuantidade": 3,
    "cotaAbertaQuantidade": 7,
    "algoritmoArredondamento": "Math.ceil (Favorecer ME/EPP)"
  }
}