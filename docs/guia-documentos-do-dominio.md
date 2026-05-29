# Guia de Documentos do Domínio — Sistema de Licitações FACAPE

> Documento de referência mantido pelo professor.
> Use este guia para entender o que cada documento real de licitação deve conter,
> quais campos são obrigatórios, quais regras de negócio se aplicam e qual grupo
> é responsável por modelá-lo no sistema.

---

## Como Usar

Cada seção deste guia descreve um documento do processo de licitações:

- **O que é**: definição objetiva do documento no domínio real.
- **Quem produz**: ator responsável pela criação.
- **Quando é produzido**: em qual fase do fluxo.
- **Grupo responsável**: qual equipe deve modelar esse documento no sistema.
- **Seções obrigatórias**: estrutura interna do documento.
- **Campos obrigatórios**: dados mínimos que cada registro deve ter.
- **Regras de negócio**: validações e restrições que o sistema deve aplicar.
- **Exemplo mínimo**: instância simples para orientar a fixture do grupo.

---

## Índice

1. [PCA — Plano de Contratação Anual](#1-pca--plano-de-contratação-anual)
2. [DFD — Documento de Formalização da Demanda](#2-dfd--documento-de-formalização-da-demanda)
3. [ETP — Estudo Técnico Preliminar](#3-etp--estudo-técnico-preliminar)
4. [Ata SRP — Ata de Registro de Preços](#4-ata-srp--ata-de-registro-de-preços)
5. [TR — Termo de Referência](#5-tr--termo-de-referência)
6. [Mapa de Riscos](#6-mapa-de-riscos)
7. [Edital](#7-edital)
8. [OF — Ordem de Fornecimento](#8-of--ordem-de-fornecimento)

---

## 1. PCA — Plano de Contratação Anual

### O que é

Documento de planejamento publicado anualmente no PNCP com a previsão de
todas as compras e contratações do exercício seguinte. Obrigatório pela
Lei 14.133/2021, serve para organizar e tornar previsível o volume de
gastos antes do início do ano fiscal.

### Quem produz

Gestor do PCA, com base nas demandas consolidadas pelo almoxarifado e
aprovação da autoridade competente.

### Quando é produzido

Até novembro do ano anterior ao exercício planejado.

### Grupo responsável

G01 — Consolidação de Demandas (o DFD alimenta o PCA).

### Seções obrigatórias

1. Identificação do órgão (nome, CNPJ, unidade gestora)
2. Exercício de referência
3. Lista de contratações previstas
4. Justificativa de cada contratação
5. Estimativa de valor por contratação
6. Dotação orçamentária prevista
7. Data de publicação

### Campos obrigatórios por item do PCA

| Campo | Tipo | Descrição |
| --- | --- | --- |
| `id_item` | texto | Identificador único do item no PCA |
| `descricao` | texto | Descrição padronizada pelo CATMAT/CATSER |
| `codigo_catmat_catser` | texto | Código do catálogo federal |
| `unidade_medida` | texto | Ex.: unidade, caixa, serviço |
| `quantidade_estimada` | número | Quantidade prevista para o exercício |
| `valor_unitario_estimado` | decimal | Valor unitário de referência |
| `valor_total_estimado` | decimal | Calculado: quantidade × valor unitário |
| `modalidade_prevista` | enum | pregao, dispensa, inexigibilidade, adesao |
| `secretaria_demandante` | texto | Unidade que gerou a necessidade |
| `justificativa` | texto | Motivo da contratação |
| `dotacao_orcamentaria` | texto | Código orçamentário da despesa |

### Regras de negócio

- **RN-03**: Itens do mesmo tipo não podem ser fracionados em múltiplas entradas do PCA para o mesmo período.
- O valor total do PCA deve ser calculado automaticamente a partir dos itens.
- A publicação no PNCP é obrigatória antes do início do exercício.

### Exemplo mínimo (fixture)

```json
{
  "pca": {
    "orgao": "FACAPE",
    "exercicio": 2026,
    "data_publicacao": "2025-11-15",
    "itens": [
      {
        "id_item": "PCA-2026-001",
        "descricao": "Papel A4 75g/m2 500 folhas",
        "codigo_catmat": "389875",
        "unidade_medida": "resma",
        "quantidade_estimada": 500,
        "valor_unitario_estimado": 28.00,
        "valor_total_estimado": 14000.00,
        "modalidade_prevista": "pregao",
        "secretaria_demandante": "Administração Geral",
        "justificativa": "Consumo histórico médio dos últimos 3 anos"
      }
    ]
  }
}
```

---

## 2. DFD — Documento de Formalização da Demanda

### O que é

Documento que consolida as necessidades de materiais e serviços de todas
as unidades da FACAPE para subsidiar um processo licitatório. É o ponto
de entrada do fluxo interno: sem DFD aprovado, nenhum processo avança.

### Quem produz

Almoxarifado (Xarifado), com base nas solicitações enviadas pelas
secretarias e colegiados.

### Quando é produzido

Após o encerramento do período de coleta de demandas de cada ciclo.

### Grupo responsável

G01 — Consolidação de Demandas.

### Seções obrigatórias

1. Identificação do DFD (número, data, exercício)
2. Relação de secretarias/unidades demandantes
3. Lista consolidada de itens
4. Histórico de consumo (anos anteriores)
5. Justificativa de variações acima de 20% em relação ao histórico
6. Assinatura do responsável pelo almoxarifado
7. Data de aprovação

### Campos obrigatórios por item

| Campo | Tipo | Descrição |
| --- | --- | --- |
| `id_item_dfd` | texto | Identificador único do item neste DFD |
| `descricao` | texto | Descrição clara e objetiva do item |
| `codigo_catmat_catser` | texto | Código de catálogo federal |
| `unidade_medida` | texto | Unidade padronizada |
| `quantidade_solicitada` | número | Total consolidado de todas as secretarias |
| `secretarias_demandantes` | lista | Quais unidades solicitaram e em qual quantidade |
| `consumo_historico_ano_anterior` | número | Quantidade consumida no exercício anterior |
| `justificativa_variacao` | texto | Obrigatório se variação > 20% |
| `status` | enum | rascunho, em_revisao, aprovado, rejeitado |

### Regras de negócio

- **RN-03**: O sistema deve detectar itens duplicados entre secretarias e consolidá-los automaticamente.
- Variações acima de 20% em relação ao histórico exigem justificativa preenchida antes da aprovação.
- O DFD só pode ser marcado como `aprovado` após validação do responsável pelo almoxarifado.
- Itens com `status = rejeitado` não avançam para o ETP.

### Exemplo mínimo (fixture)

```json
{
  "dfd": {
    "numero": "DFD-2026-0012",
    "data_abertura": "2026-01-10",
    "exercicio": 2026,
    "responsavel": "João Almoxarifado",
    "status": "aprovado",
    "itens": [
      {
        "id_item_dfd": "DFD-2026-0012-001",
        "descricao": "Papel A4 75g/m2 500 folhas",
        "codigo_catmat": "389875",
        "unidade_medida": "resma",
        "quantidade_solicitada": 480,
        "secretarias_demandantes": [
          { "secretaria": "Administração Geral", "quantidade": 200 },
          { "secretaria": "Coordenação de Cursos", "quantidade": 280 }
        ],
        "consumo_historico_ano_anterior": 400,
        "justificativa_variacao": "Aumento de 20% pela abertura de 2 novos cursos em 2026",
        "status": "aprovado"
      }
    ]
  }
}
```

---

## 3. ETP — Estudo Técnico Preliminar

### O que é

Documento que justifica a necessidade da contratação, analisa as
soluções disponíveis no mercado e fundamenta tecnicamente a escolha
da modalidade e do objeto a ser contratado. Obrigatório pela Lei
14.133/2021 antes da elaboração do Termo de Referência.

### Quem produz

Setor de Compras / Chefe de Licitações, com apoio técnico das secretarias
demandantes.

### Quando é produzido

Após a aprovação do DFD, antes da elaboração do TR.

### Grupo responsável

G02 — Estudo Técnico Preliminar.

### Seções obrigatórias

1. Identificação do ETP (número, data, referência ao DFD)
2. Descrição da necessidade
3. Análise de soluções disponíveis no mercado
4. Pesquisa de preços (mínimo 3 cotações por item)
5. Estimativa de valor total
6. Recomendação de modalidade (pregão, dispensa, adesão a ata)
7. Recomendação de parcelamento (sim/não + justificativa)
8. Justificativa técnica da escolha
9. Informações sobre sustentabilidade (quando aplicável)
10. Assinatura do responsável

### Campos obrigatórios por item

| Campo | Tipo | Descrição |
| --- | --- | --- |
| `id_item_etp` | texto | Referência ao item do DFD |
| `descricao_refinada` | texto | Especificação técnica detalhada (normas, padrões) |
| `cotacoes` | lista | Mínimo 3 cotações de fontes distintas |
| `valor_referencia` | decimal | Mediana das cotações obtidas |
| `fonte_preco` | texto | Banco de Preços, PNCP, fornecedor direto, etc. |
| `modalidade_recomendada` | enum | pregao, dispensa, inexigibilidade, adesao_srp |
| `justificativa_modalidade` | texto | Fundamento legal e técnico |
| `parcelamento_recomendado` | booleano | Se os itens devem ser licitados em lotes separados |
| `justificativa_parcelamento` | texto | Obrigatório se parcelamento = true |

### Campos obrigatórios por cotação

| Campo | Tipo | Descrição |
| --- | --- | --- |
| `fornecedor` | texto | Nome ou CNPJ do fornecedor consultado |
| `data_cotacao` | data | Data da consulta |
| `valor_unitario` | decimal | Valor cotado por unidade |
| `fonte` | texto | Como a cotação foi obtida (banco de preços, site, e-mail) |

### Regras de negócio

- **RN-10**: Cada item deve ter no mínimo 3 cotações de fontes distintas.
- O sistema deve calcular o valor de referência como a mediana das cotações.
- Cotações com desvio acima de 50% da mediana devem ser sinalizadas como outlier.
- A modalidade `dispensa` só pode ser recomendada se o valor total estimado for ≤ R$ 57.000 (2026).
- A modalidade `adesao_srp` só pode ser recomendada se houver ata vigente compatível (verificado pelo G03).

### Exemplo mínimo (fixture)

```json
{
  "etp": {
    "numero": "ETP-2026-0012",
    "data": "2026-01-20",
    "referencia_dfd": "DFD-2026-0012",
    "responsavel": "Maria Compras",
    "itens": [
      {
        "id_item_etp": "ETP-2026-0012-001",
        "referencia_dfd": "DFD-2026-0012-001",
        "descricao_refinada": "Papel A4 75g/m2, alvura mínima 91%, 500 folhas por resma, conforme ABNT NBR 15967",
        "cotacoes": [
          { "fornecedor": "Papelaria Sul", "data_cotacao": "2026-01-15", "valor_unitario": 27.50, "fonte": "banco_de_precos" },
          { "fornecedor": "PNCP processo 2025", "data_cotacao": "2026-01-15", "valor_unitario": 28.10, "fonte": "pncp" },
          { "fornecedor": "Atacado PE Ltda", "data_cotacao": "2026-01-16", "valor_unitario": 27.80, "fonte": "email_fornecedor" }
        ],
        "valor_referencia": 27.80,
        "modalidade_recomendada": "pregao",
        "justificativa_modalidade": "Bem comum com ampla concorrência; pregão eletrônico garante menor preço (art. 6º, XLI, Lei 14.133/2021)",
        "parcelamento_recomendado": false,
        "justificativa_parcelamento": "Item único sem necessidade de divisão por lote"
      }
    ]
  }
}
```

---

## 4. Ata SRP — Ata de Registro de Preços

### O que é

Instrumento contratual resultante de um pregão do tipo SRP (Sistema de
Registro de Preços). Registra a empresa vencedora, os itens, os preços
e os quantitativos máximos para um prazo de 12 meses. A FACAPE pode
comprar qualquer quantidade dentro desse limite, sem obrigação de
atingir o total registrado.

### Quem produz

A Prefeitura (após o pregão); a FACAPE consulta e utiliza atas vigentes.

### Quando é produzido

Após a homologação do pregão pela Prefeitura.

### Grupo responsável

G03 — Pesquisa e Gestão de Atas SRP.

### Campos obrigatórios

| Campo | Tipo | Descrição |
| --- | --- | --- |
| `numero_ata` | texto | Número único da ata |
| `orgao_gerenciador` | texto | Órgão que realizou o pregão original |
| `data_assinatura` | data | Data de assinatura da ata |
| `data_vencimento` | data | Data de expiração (máximo 12 meses da assinatura) |
| `empresa_fornecedora` | texto | Nome e CNPJ da empresa vencedora |
| `itens` | lista | Itens registrados com preço e saldo |
| `status` | enum | vigente, expirada, suspensa, cancelada |

### Campos obrigatórios por item da ata

| Campo | Tipo | Descrição |
| --- | --- | --- |
| `codigo_catmat` | texto | Código do catálogo federal |
| `descricao` | texto | Especificação do item registrado |
| `unidade_medida` | texto | Unidade de referência |
| `quantidade_registrada` | número | Quantitativo total registrado |
| `quantidade_consumida` | número | Total já adquirido por adesão |
| `saldo_disponivel` | número | Calculado: registrada - consumida |
| `valor_unitario_registrado` | decimal | Preço registrado na ata |

### Regras de negócio

- **RN-04**: Contratos SRP não obrigam a comprar o quantitativo total registrado (pode-se comprar 0% a 100%).
- **RN-08**: Adesão por carona é limitada a 50% do quantitativo original da ata.
- Uma ata com `status ≠ vigente` não pode ser usada para novas ordens de fornecimento.
- O sistema deve alertar quando o saldo disponível de um item cair abaixo de 20%.
- O sistema deve alertar quando a `data_vencimento` estiver a 30 dias ou menos.

### Exemplo mínimo (fixture)

```json
{
  "ata_srp": {
    "numero_ata": "ATA-SRP-2025-007",
    "orgao_gerenciador": "Prefeitura de Petrolina",
    "data_assinatura": "2025-03-01",
    "data_vencimento": "2026-03-01",
    "empresa_fornecedora": { "nome": "Papelaria Sul Ltda", "cnpj": "12.345.678/0001-99" },
    "status": "vigente",
    "itens": [
      {
        "codigo_catmat": "389875",
        "descricao": "Papel A4 75g/m2 500 folhas",
        "unidade_medida": "resma",
        "quantidade_registrada": 2000,
        "quantidade_consumida": 600,
        "saldo_disponivel": 1400,
        "valor_unitario_registrado": 26.50
      }
    ]
  }
}
```

---

## 5. TR — Termo de Referência

### O que é

Documento técnico-legal que especifica com precisão o objeto a ser
contratado. Integra obrigatoriamente o edital e define as condições
de execução, critérios de aceitação, prazos, penalidades e as regras
de participação de ME/EPP. É o documento que as empresas usam para
formular suas propostas.

### Quem produz

Setor de Licitações, com base no ETP aprovado e na decisão sobre uso de
atas SRP.

### Quando é produzido

Após aprovação do ETP e análise de atas SRP (G02 e G03), antes do envio
à Prefeitura.

### Grupo responsável

G04 — Elaboração de Termo de Referência.

### Seções obrigatórias

1. Identificação do TR (número, data, referências ao ETP e DFD)
2. Objeto da contratação (descrição clara e objetiva)
3. Justificativa da necessidade
4. Especificação técnica por item/lote
5. Divisão por cotas (exclusiva ME/EPP e aberta, quando aplicável)
6. Critério de julgamento (menor preço por item, menor preço global, etc.)
7. Critérios de aceitação (como a entrega será validada)
8. Prazo de entrega
9. Local de entrega
10. Obrigações do contratante
11. Obrigações do contratado
12. Penalidades por inadimplemento
13. Condições de pagamento
14. Vedações e restrições
15. Assinatura do responsável e aprovação jurídica

### Campos obrigatórios por item/lote

| Campo | Tipo | Descrição |
| --- | --- | --- |
| `id_item_tr` | texto | Identificador único no TR |
| `referencia_etp` | texto | ID do item no ETP correspondente |
| `descricao_completa` | texto | Especificação técnica detalhada |
| `unidade_medida` | texto | |
| `quantidade` | número | |
| `valor_unitario_estimado` | decimal | Valor de referência do ETP |
| `tipo_cota` | enum | exclusiva_mepp, aberta, sem_cota |
| `percentual_cota_mepp` | decimal | 100% (exclusiva) ou 25% (cota reservada) |
| `criterio_aceitacao` | texto | Como será verificada a conformidade na entrega |
| `prazo_entrega_dias` | número | Dias corridos a partir da OF |
| `local_entrega` | texto | Endereço/setor de recebimento |
| `permite_subcontratacao` | booleano | Sempre false para FACAPE |

### Regras de negócio

- **RN-01**: Itens com valor estimado ≤ R$ 80.000 devem ter `tipo_cota = exclusiva_mepp`.
- **RN-02**: Itens com valor estimado > R$ 80.000 devem ter divisão: 25% com `tipo_cota = exclusiva_mepp` e 75% com `tipo_cota = aberta`.
- **RN-09**: `permite_subcontratacao` deve sempre ser `false`.
- O sistema deve calcular automaticamente a divisão de cotas com base no valor estimado.
- O TR só pode ser marcado como `aprovado` após validação jurídica.

### Exemplo mínimo (fixture)

```json
{
  "tr": {
    "numero": "TR-2026-0012",
    "data": "2026-02-05",
    "referencia_etp": "ETP-2026-0012",
    "responsavel": "Carlos Licitações",
    "status": "aprovado",
    "itens": [
      {
        "id_item_tr": "TR-2026-0012-001",
        "referencia_etp": "ETP-2026-0012-001",
        "descricao_completa": "Papel A4 75g/m2, alvura mínima 91%, 500 folhas, conforme ABNT NBR 15967",
        "unidade_medida": "resma",
        "quantidade": 480,
        "valor_unitario_estimado": 27.80,
        "tipo_cota": "exclusiva_mepp",
        "percentual_cota_mepp": 100,
        "criterio_aceitacao": "Conferência física de gramatura, alvura e quantidade por amostragem de 10%",
        "prazo_entrega_dias": 15,
        "local_entrega": "Almoxarifado FACAPE — Av. Domingos Peretti s/n",
        "permite_subcontratacao": false
      }
    ]
  }
}
```

---

## 6. Mapa de Riscos

### O que é

Documento obrigatório pela Lei 14.133/2021 que identifica, classifica e
propõe mitigação para os riscos da contratação. Foca nos riscos do
**objeto** (produto ou serviço a ser contratado), não nos riscos do
processo licitatório em si.

### Quem produz

Setor de Compras / Chefe de Licitações.

### Quando é produzido

Em paralelo ou após o TR, antes do envio à Prefeitura.

### Grupo responsável

G05 — Mapa de Riscos.

### Seções obrigatórias

1. Identificação do documento (número, data, referência ao TR)
2. Metodologia de avaliação (ex.: matriz 3×3, 5×5)
3. Lista de riscos identificados
4. Matriz de probabilidade × impacto
5. Plano de mitigação por risco
6. Responsável por cada mitigação
7. Assinatura

### Campos obrigatórios por risco

| Campo | Tipo | Descrição |
| --- | --- | --- |
| `id_risco` | texto | Identificador único do risco |
| `categoria` | enum | preco, especificacao, mercado, ambiental, manutencao, legal |
| `descricao` | texto | Descrição objetiva do risco |
| `probabilidade` | enum | baixa, media, alta |
| `impacto` | enum | baixo, medio, alto |
| `nivel_risco` | enum | baixo, medio, alto, critico — calculado pela matriz |
| `acao_mitigadora` | texto | O que fazer para reduzir o risco |
| `responsavel_mitigacao` | texto | Quem deve executar a ação |
| `status` | enum | identificado, mitigado, aceito, transferido |

### Matriz de nível de risco (3×3)

|  | Impacto Baixo | Impacto Médio | Impacto Alto |
| --- | --- | --- | --- |
| **Probabilidade Alta** | medio | alto | critico |
| **Probabilidade Média** | baixo | medio | alto |
| **Probabilidade Baixa** | baixo | baixo | medio |

### Regras de negócio

- Riscos com `nivel_risco = critico` não permitem que o processo avance sem ação mitigadora documentada.
- O sistema deve calcular `nivel_risco` automaticamente a partir de `probabilidade` × `impacto`.
- Os riscos "processo deserto" e "processo fracassado" são obrigatórios em qualquer Mapa de Riscos.

### Exemplo mínimo (fixture)

```json
{
  "mapa_riscos": {
    "numero": "RISCO-2026-0012",
    "data": "2026-02-10",
    "referencia_tr": "TR-2026-0012",
    "metodologia": "Matriz 3x3 (probabilidade × impacto)",
    "riscos": [
      {
        "id_risco": "R001",
        "categoria": "mercado",
        "descricao": "Processo deserto por falta de fornecedores interessados",
        "probabilidade": "baixa",
        "impacto": "alto",
        "nivel_risco": "medio",
        "acao_mitigadora": "Ampliar prazo de divulgação e contatar fornecedores previamente",
        "responsavel_mitigacao": "Chefe de Licitações",
        "status": "identificado"
      },
      {
        "id_risco": "R002",
        "categoria": "preco",
        "descricao": "Cotações desatualizadas levando a valor de referência abaixo do mercado",
        "probabilidade": "media",
        "impacto": "alto",
        "nivel_risco": "alto",
        "acao_mitigadora": "Refazer cotações em período máximo de 30 dias antes da publicação",
        "responsavel_mitigacao": "Setor de Compras",
        "status": "identificado"
      }
    ]
  }
}
```

---

## 7. Edital

### O que é

Documento público que formaliza e divulga o processo licitatório ao
mercado. Elaborado e publicado pela **Prefeitura de Petrolina**, com
base no TR enviado pela FACAPE. O sistema da FACAPE **não elabora o
edital** — apenas envia o pacote de documentos (TR + Mapa de Riscos)
para que a Prefeitura o elabore.

### Atenção para os grupos

> O edital está **fora do escopo de implementação**. O G06 modela o
> envio do pacote à Prefeitura e o G07 acompanha o status do processo
> externo. Nenhum grupo deve implementar a lógica de elaboração do
> edital.

### Grupos responsáveis

- G06 — Preparação de Edital: monta e valida o pacote enviado à Prefeitura.
- G07 — Acompanhamento Externo: consulta o status do edital publicado.

### Campos que o G06 deve rastrear no pacote enviado

| Campo | Tipo | Descrição |
| --- | --- | --- |
| `numero_pacote` | texto | Identificador do envio |
| `data_envio` | data | Data e hora do envio à Prefeitura |
| `referencia_tr` | texto | TR incluído no pacote |
| `referencia_mapa_riscos` | texto | Mapa de Riscos incluído |
| `canal_envio` | texto | Ex.: 1doc, e-mail, protocolo físico |
| `numero_protocolo_externo` | texto | Número gerado pela Prefeitura no recebimento |
| `confirmacao_recebimento` | booleano | Se a Prefeitura confirmou o recebimento |
| `status` | enum | enviado, recebido, em_analise, publicado, devolvido |

### Campos que o G07 deve acompanhar (status externo)

| Campo | Tipo | Descrição |
| --- | --- | --- |
| `numero_pregao` | texto | Número do pregão no sistema da Prefeitura |
| `data_abertura_sessao` | data | Data e hora da sessão do pregão |
| `status_pregao` | enum | publicado, aberto, em_julgamento, adjudicado, homologado, deserto, fracassado |
| `empresa_vencedora` | texto | Nome e CNPJ (preenchido após adjudicação) |
| `valor_final_homologado` | decimal | Valor total após o pregão |
| `data_ultima_atualizacao` | data | Última consulta ao sistema externo |

### Regras de negócio

- **RN-07**: O edital deve ser publicado no PNCP por no mínimo 8 dias úteis antes da sessão do pregão.
- O G06 não pode marcar o pacote como `enviado` sem o checklist de completude 100% preenchido.
- O G07 deve registrar cada mudança de `status_pregao` com data e fonte da informação.

---

## 8. OF — Ordem de Fornecimento

### O que é

Documento emitido pela FACAPE ao fornecedor vencedor do pregão,
solicitando formalmente a entrega dos itens adjudicados. É o
instrumento que aciona a execução: sem OF emitida e confirmada, a
empresa não deve entregar nem a FACAPE pagar.

### Quem produz

Setor de Compras, após a homologação do pregão e com base na ata SRP
ou contrato.

### Quando é produzido

Após homologação do pregão (G07 confirma resultado) e antes de qualquer
entrega pelo fornecedor.

### Grupo responsável

G08 — Controle de Ordem de Fornecimento.

### Campos obrigatórios

| Campo | Tipo | Descrição |
| --- | --- | --- |
| `numero_of` | texto | Número único da OF (ex.: OF-2026-0042) |
| `data_emissao` | data | Data de geração da OF |
| `referencia_ata_ou_contrato` | texto | Ata SRP ou contrato que ampara a compra |
| `empresa_fornecedora` | texto | Nome e CNPJ |
| `responsavel_emissao` | texto | Quem gerou a OF no sistema |
| `itens` | lista | Itens solicitados nesta OF |
| `valor_total_of` | decimal | Soma dos itens |
| `prazo_entrega` | data | Data limite para entrega |
| `local_entrega` | texto | Endereço de recebimento |
| `condicoes_pagamento` | texto | Ex.: 30 dias após nota fiscal |
| `status` | enum | emitida, enviada, confirmada_pelo_fornecedor, entregue, paga, cancelada |
| `data_confirmacao_recebimento` | data | Quando o fornecedor confirmou a OF |

### Campos obrigatórios por item da OF

| Campo | Tipo | Descrição |
| --- | --- | --- |
| `id_item_of` | texto | Identificador do item nesta OF |
| `referencia_item_ata` | texto | Item da ata ou contrato que originou |
| `descricao` | texto | Descrição do item |
| `quantidade` | número | Quantidade solicitada |
| `valor_unitario` | decimal | Preço registrado na ata |
| `valor_total_item` | decimal | Calculado: quantidade × valor unitário |
| `status_entrega` | enum | pendente, entregue_parcial, entregue_total, rejeitado |

### Regras de negócio

- **RN-04**: O saldo acumulado de OFs de uma ata SRP não pode ultrapassar 100% do quantitativo registrado.
- **RN-05**: Para contratos diretos (não SRP), a FACAPE é obrigada a emitir OFs que totalizem ≥ 75% do contratado.
- Uma OF só pode ser emitida para empresa com ata ou contrato vigente (`status = vigente`).
- O `valor_total_of` deve ser calculado automaticamente e não pode exceder o saldo disponível na ata.
- OFs com `status = cancelada` devem devolver o saldo ao item da ata.

### Exemplo mínimo (fixture)

```json
{
  "of": {
    "numero_of": "OF-2026-0042",
    "data_emissao": "2026-04-10",
    "referencia_ata_ou_contrato": "ATA-SRP-2025-007",
    "empresa_fornecedora": { "nome": "Papelaria Sul Ltda", "cnpj": "12.345.678/0001-99" },
    "responsavel_emissao": "Maria Compras",
    "prazo_entrega": "2026-04-25",
    "local_entrega": "Almoxarifado FACAPE — Av. Domingos Peretti s/n",
    "condicoes_pagamento": "30 dias após apresentação de nota fiscal",
    "status": "enviada",
    "itens": [
      {
        "id_item_of": "OF-2026-0042-001",
        "referencia_item_ata": "ATA-SRP-2025-007 / item 001",
        "descricao": "Papel A4 75g/m2 500 folhas",
        "quantidade": 200,
        "valor_unitario": 26.50,
        "valor_total_item": 5300.00,
        "status_entrega": "pendente"
      }
    ],
    "valor_total_of": 5300.00
  }
}
```

---

## Resumo: Fluxo dos Documentos

```
PCA (G01)
  ↓ alimenta planejamento
DFD (G01)
  ↓
ETP (G02) ← consulta Ata SRP (G03)
  ↓
TR (G04) ← usa ETP + resultado da análise de atas
  ↓
Mapa de Riscos (G05) ← usa TR
  ↓
Pacote enviado à Prefeitura (G06): TR + Mapa de Riscos
  ↓ [FORA DO SISTEMA — Prefeitura elabora o Edital]
G07 acompanha status externo → recebe empresa vencedora + valor final
  ↓
OF (G08) ← usa resultado do G07 e saldo da Ata SRP
  ↓
G09 audita todos os eventos acima
```

---

## Referências Normativas

| Norma | Relevância |
| --- | --- |
| Lei Federal nº 14.133/2021 | Lei de Licitações e Contratos; regula todo o processo |
| Lei Complementar nº 123/2006 | Tratamento diferenciado ME/EPP; regras de cota e empate |
| Decreto Federal nº 11.462/2023 | Regulamenta o SRP; define limites de adesão |
| IN SEGES nº 65/2021 | Pesquisa de preços para aquisição de bens e serviços |

---

## Checklist de Validação por Documento

O sistema deve aplicar estas verificações antes de permitir que um documento
avance de fase. Cada item é uma regra que o grupo responsável deve implementar
e testar.

### PCA

- [ ] Todos os itens possuem código CATMAT/CATSER preenchido.
- [ ] Nenhum item do mesmo tipo aparece duplicado no mesmo exercício (RN-03).
- [ ] `valor_total_estimado` de cada item é igual a `quantidade_estimada × valor_unitario_estimado`.
- [ ] `dotacao_orcamentaria` preenchida para todos os itens.

### DFD

- [ ] Nenhum item duplicado entre secretarias — devem estar consolidados.
- [ ] Itens com variação > 20% em relação ao `consumo_historico_ano_anterior` possuem `justificativa_variacao` não vazia.
- [ ] Todos os itens possuem pelo menos uma secretaria demandante na lista.
- [ ] O DFD está assinado (campo `responsavel` preenchido) antes de mudar para `aprovado`.
- [ ] Itens com `status = rejeitado` não foram incluídos na lista de saída para o ETP.

### ETP

- [ ] Cada item possui no mínimo 3 cotações de fontes distintas (RN-10).
- [ ] Nenhum par de cotações do mesmo item tem o mesmo `fornecedor`.
- [ ] `valor_referencia` é a mediana das cotações (não a média, não o menor valor).
- [ ] Cotações com desvio > 50% da mediana estão sinalizadas como `outlier = true`.
- [ ] Se `modalidade_recomendada = dispensa`, o `valor_total_estimado` do item é ≤ R$ 57.000.
- [ ] Se `modalidade_recomendada = adesao_srp`, existe ata vigente compatível confirmada pelo G03.
- [ ] Se `parcelamento_recomendado = true`, `justificativa_parcelamento` está preenchida.

### Ata SRP

- [ ] `data_vencimento` é no máximo 12 meses após `data_assinatura`.
- [ ] `saldo_disponivel` de cada item é igual a `quantidade_registrada - quantidade_consumida`.
- [ ] O saldo não é negativo em nenhum item.
- [ ] Atas com `status ≠ vigente` não aparecem como opção de uso em novas OFs.

### TR

- [ ] Itens com `valor_unitario_estimado × quantidade ≤ R$ 80.000` têm `tipo_cota = exclusiva_mepp` (RN-01).
- [ ] Itens com `valor_unitario_estimado × quantidade > R$ 80.000` possuem divisão de lotes: 25% exclusiva + 75% aberta (RN-02).
- [ ] Nenhum item tem `permite_subcontratacao = true` (RN-09).
- [ ] `criterio_aceitacao` está preenchido para todos os itens.
- [ ] O TR só pode ser marcado como `aprovado` se houver registro de validação jurídica.

### Mapa de Riscos

- [ ] O risco "processo deserto" está presente.
- [ ] O risco "processo fracassado" está presente.
- [ ] `nivel_risco` de cada risco é calculado conforme a matriz (não preenchido manualmente).
- [ ] Riscos com `nivel_risco = critico` possuem `acao_mitigadora` e `responsavel_mitigacao` preenchidos.
- [ ] Nenhum risco crítico está com `status = identificado` sem ação associada.

### Pacote de Envio (G06)

- [ ] TR referenciado está com `status = aprovado`.
- [ ] Mapa de Riscos referenciado está com `status ≠ rascunho`.
- [ ] `canal_envio` está preenchido.
- [ ] O pacote não pode ser marcado como `enviado` sem `confirmacao_recebimento = true`.

### OF

- [ ] A ata ou contrato referenciado está com `status = vigente`.
- [ ] A quantidade solicitada na OF não ultrapassa o `saldo_disponivel` da ata (RN-04).
- [ ] `valor_total_of` é igual à soma dos `valor_total_item` dos itens.
- [ ] `prazo_entrega` é posterior à `data_emissao`.
- [ ] OFs canceladas devolvem o saldo ao item da ata automaticamente.

---

## Diagrama de Estados por Documento

Cada documento percorre um ciclo de vida no sistema. Os estados abaixo
definem as transições permitidas e quem pode executá-las.

### DFD

```
rascunho ──(submeter)──► em_revisao ──(aprovar)──► aprovado
                                  └──(rejeitar)──► rejeitado
rejeitado ──(corrigir)──► em_revisao
```

- `rascunho → em_revisao`: qualquer secretaria demandante ao finalizar o preenchimento.
- `em_revisao → aprovado`: exclusivo do responsável pelo almoxarifado.
- `em_revisao → rejeitado`: responsável pelo almoxarifado, com motivo obrigatório.
- `rejeitado → em_revisao`: secretaria demandante, após correção.

### ETP

```
rascunho ──(submeter)──► em_analise ──(aprovar)──► aprovado
                                   └──(devolver)──► rascunho
```

- `em_analise → aprovado`: chefe de compras.
- `em_analise → rascunho`: chefe de compras, com comentário de devolução obrigatório.

### TR

```
rascunho ──(submeter)──► aguardando_juridico ──(validar)──► aprovado
                                             └──(devolver)──► rascunho
```

- `aguardando_juridico → aprovado`: jurídico interno.
- `aguardando_juridico → rascunho`: jurídico interno, com apontamentos obrigatórios.

### Mapa de Riscos

```
rascunho ──(finalizar)──► revisao ──(aprovar)──► aprovado
```

### Ata SRP

```
vigente ──(expirar/cancelar)──► expirada | cancelada | suspensa
vigente ──(saldo zerado)──► esgotada
```

- Transições de `vigente` para outros estados são automáticas ou executadas pelo sistema após confirmação da Prefeitura.
- Atas `expiradas`, `canceladas` ou `suspensas` são somente leitura.

### OF

```
emitida ──(enviar)──► enviada ──(fornecedor confirma)──► confirmada_pelo_fornecedor
confirmada_pelo_fornecedor ──(entregar)──► entregue ──(pagar)──► paga
enviada ──(cancelar)──► cancelada
confirmada_pelo_fornecedor ──(cancelar)──► cancelada
```

- `cancelada` devolve saldo à ata automaticamente.
- `paga` é estado terminal — não há transição de saída.

### Pacote de Envio (G06)

```
montado ──(validar checklist)──► pronto ──(enviar)──► enviado
enviado ──(prefeitura confirma)──► recebido ──(prefeitura analisa)──► em_analise
em_analise ──(publicar)──► publicado
em_analise ──(devolver)──► devolvido ──(corrigir)──► montado
```

### Status do Pregão (G07 — somente leitura)

```
publicado ──► aberto ──► em_julgamento ──► adjudicado ──► homologado
                                       └──► deserto
                                       └──► fracassado
```

---

## Dependências Entre Documentos

A tabela abaixo define qual documento bloqueia qual, impedindo o avanço
do processo quando a dependência não está satisfeita.

| Documento | Depende de | Condição obrigatória | Regra |
| --- | --- | --- | --- |
| DFD | — | — | Ponto de entrada; não tem dependência anterior |
| ETP | DFD | `status = aprovado` | Sem DFD aprovado o ETP não pode ser iniciado |
| Análise de Atas SRP | ETP | `status = aprovado` | A análise precisa da especificação refinada do ETP |
| TR | ETP | `status = aprovado` | TR herda especificação técnica do ETP |
| TR | Análise de Atas SRP | resultado registrado | TR precisa saber se há ata viável para definir modalidade |
| Mapa de Riscos | TR | `status = aprovado` | Riscos são avaliados sobre o objeto já especificado no TR |
| Pacote de Envio | TR | `status = aprovado` | TR validado juridicamente é requisito do pacote |
| Pacote de Envio | Mapa de Riscos | `status = aprovado` | Mapa obrigatório na composição do pacote (Lei 14.133) |
| Acompanhamento externo (G07) | Pacote de Envio | `status = recebido` | Só faz sentido acompanhar após a Prefeitura receber |
| OF | Resultado do pregão (G07) | `status_pregao = homologado` | OF só pode ser emitida após homologação |
| OF | Ata SRP | `status = vigente` e `saldo_disponivel > 0` | RN-04 |
| Auditoria (G09) | Todos os documentos | qualquer evento de mudança de estado | G09 escuta todos; não bloqueia, mas deve registrar cada transição |

### Regras de bloqueio importantes

- Um grupo **não pode avançar** para o próximo estado sem a dependência satisfeita.
- Dependências não satisfeitas devem gerar mensagem de erro clara (ex.: `"ETP-2026-0012 ainda está em análise — TR não pode ser iniciado"`).
- O G09 deve registrar tentativas de avanço bloqueadas como eventos de auditoria.

---

## Erros Comuns e Como Evitar

Baseados nos gargalos reais levantados na aula com a especialista do setor
de licitações da FACAPE (Carla Vanessa, abril de 2026).

### 1. Fracionamento de despesa (RN-03)

**O que acontece:** Duas secretarias solicitam o mesmo item separadamente, gerando dois processos distintos para o mesmo objeto no mesmo exercício. Isso é ilegal.

**Como o sistema deve evitar:** Ao receber demandas com o mesmo `codigo_catmat_catser` no mesmo exercício, o sistema deve consolidá-las automaticamente em um único item do DFD, somando as quantidades e registrando todas as secretarias na lista `secretarias_demandantes`.

**Teste obrigatório (G01):** Inserir duas demandas com o mesmo CATMAT de secretarias distintas e verificar que o DFD gerado contém apenas um item com a soma das quantidades.

---

### 2. Cotações desatualizadas no ETP

**O que acontece:** A pesquisa de preços é feita com muita antecedência, mas o processo demora e os valores de mercado mudam. O TR vai a leilão com preço de referência abaixo do praticado, resultando em processo deserto.

**Como o sistema deve evitar:** O sistema deve registrar a `data_cotacao` de cada fonte e alertar quando qualquer cotação de um item tiver mais de 90 dias. O alerta deve aparecer antes da aprovação do ETP.

**Teste obrigatório (G02):** Criar um ETP com cotação de data superior a 90 dias e verificar que o sistema bloqueia a aprovação com mensagem de aviso.

---

### 3. Especificação técnica genérica ou restritiva no TR

**O que acontece:** Especificações muito genéricas permitem entrega de produto de baixa qualidade. Especificações que citam marca específica são ilegais (direcionamento) e causam impugnação de empresa, gerando republicação (~R$ 5.000 por processo).

**Como o sistema deve evitar:** O `criterio_aceitacao` do TR deve ser obrigatório e descritivo. O sistema deve recusar TRs cujo `criterio_aceitacao` esteja vazio ou com menos de 30 caracteres. Menções a marcas específicas não podem ser validadas automaticamente, mas o checklist do grupo deve incluir verificação manual.

**Teste obrigatório (G04):** Tentar aprovar um TR com `criterio_aceitacao` vazio e verificar que o sistema rejeita.

---

### 4. Uso de ata SRP expirada ou com saldo zerado

**O que acontece:** Uma OF é emitida referenciando uma ata cujo prazo de 12 meses já venceu, ou cujo saldo foi totalmente consumido. A entrega ocorre sem amparo contratual.

**Como o sistema deve evitar:** O sistema deve verificar `status = vigente` e `saldo_disponivel > 0` antes de permitir a emissão de qualquer OF. Atas com vencimento em 30 dias devem gerar alerta proativo ao responsável.

**Teste obrigatório (G08):** Tentar emitir uma OF para uma ata com `status = expirada` e verificar que o sistema bloqueia com mensagem clara.

---

### 5. Ordem de Fornecimento em papel sem rastreabilidade

**O que acontece:** A OF é impressa, assinada à mão e entregue ao fornecedor. Não há registro de quando foi enviada, se foi confirmada ou qual item foi efetivamente entregue. Auditoria não consegue reconstruir o histórico.

**Como o sistema deve evitar:** Toda OF deve ter `data_emissao`, `status` rastreável e `data_confirmacao_recebimento`. Nenhuma OF deve ir para `entregue` sem passar por `confirmada_pelo_fornecedor`. O G09 deve registrar cada transição de estado da OF como evento imutável.

**Teste obrigatório (G08):** Tentar marcar uma OF como `entregue` sem antes marcar como `confirmada_pelo_fornecedor` e verificar que o sistema recusa a transição.

---

### 6. Adesão a ata além do limite legal (RN-08)

**O que acontece:** A FACAPE tenta comprar mais do que 50% do quantitativo original da ata de outro órgão (carona). Isso é ilegal e pode anular a compra.

**Como o sistema deve evitar:** Ao registrar adesão por carona, o sistema deve calcular o total já aderido por todos os órgãos e bloquear quando a soma ultrapassar 50% do `quantidade_registrada` original.

**Teste obrigatório (G03):** Simular adesão que ultrapassaria 50% do quantitativo da ata e verificar que o sistema bloqueia com cálculo do limite exibido.
