# Casos de Uso — G3 Gestão de Atas SRP

**Ator principal:** Agente de Compras / Chefe do Setor de Licitações  
**Ator secundário:** Sistema (G2 - ETP, G4 - TR)

---

## Diagrama de Casos de Uso

```
                    Sistema de Gestão de Atas SRP

  Agente de Compras ──── UC01: Pesquisar Atas Vigentes
                    │
                    ├──── UC02: Verificar Compatibilidade de Item
                    │
                    ├──── UC03: Calcular Saldo Disponível da Ata
                    │
                    ├──── UC04: Calcular Limite de Carona
                    │
                    ├──── UC05: Registrar Ata Selecionada
                    │
                    └──── UC06: Exportar Resultado para G4 (TR)

  Sistema G2 (ETP) ──── (fornece dados de entrada) ──── UC01
  Sistema G4 (TR)  ──── (consome dados de saída)   ──── UC06
```

---

## UC01 — Pesquisar Atas Vigentes

| Campo | Descrição |
|---|---|
| **Ator** | Agente de Compras |
| **Pré-condição** | ETP recebido do G2, contendo descrição do item/serviço |
| **Fluxo Principal** | 1. Agente informa o item/categoria a partir do ETP<br>2. Sistema busca atas com status "vigente" (data atual ≤ data de validade)<br>3. Sistema filtra atas cujo item registrado seja compatível com o item buscado<br>4. Sistema lista as atas encontradas, ordenadas por menor preço unitário |
| **Fluxo Alternativo** | 4a. Nenhuma ata encontrada → sistema gera mensagem "Nenhuma ata compatível" e recomenda seguir fluxo de nova licitação (RN-10) |
| **Pós-condição** | Lista de atas candidatas disponível para análise |

---

## UC02 — Verificar Compatibilidade de Item

| Campo | Descrição |
|---|---|
| **Ator** | Sistema (automático, parte do UC01) |
| **Descrição** | Compara especificação técnica do item do ETP com a especificação registrada na ata, usando categoria + unidade de medida + descrição |
| **Regra** | Item é compatível se categoria e unidade de medida coincidem e a descrição tem similaridade ≥ a um limiar definido |

---

## UC03 — Calcular Saldo Disponível da Ata

| Campo | Descrição |
|---|---|
| **Ator** | Agente de Compras |
| **Pré-condição** | Ata selecionada da lista de UC01 |
| **Fluxo Principal** | 1. Sistema recupera quantidade total registrada na ata<br>2. Sistema recupera soma das quantidades já empenhadas<br>3. Sistema calcula: `saldo = quantidade_registrada − quantidade_empenhada`<br>4. Sistema exibe saldo e percentual já consumido |
| **Regra aplicada** | RN-04 — não há mínimo obrigatório de consumo |
| **Pós-condição** | Saldo disponível exibido |

---

## UC04 — Calcular Limite de Carona

| Campo | Descrição |
|---|---|
| **Ator** | Agente de Compras |
| **Pré-condição** | Ata selecionada pertence a outro órgão (não é ata própria da FACAPE) |
| **Fluxo Principal** | 1. Sistema identifica que a ata é de terceiros (origem ≠ FACAPE)<br>2. Sistema calcula: `limite_carona = quantidade_registrada × 0.5`<br>3. Sistema calcula: `disponível_para_carona = limite_carona − quantidade_já_aderida_por_caronas`<br>4. Sistema valida se a quantidade desejada pela FACAPE ≤ disponível_para_carona |
| **Fluxo Alternativo** | 4a. Quantidade desejada > disponível → sistema bloqueia e exibe alerta de violação da RN-08 |
| **Regra aplicada** | RN-08 — limite de 50% do quantitativo original |
| **Pós-condição** | Validação de viabilidade da carona |

---

## UC05 — Registrar Ata Selecionada

| Campo | Descrição |
|---|---|
| **Ator** | Agente de Compras |
| **Pré-condição** | Ata validada (UC03 e/ou UC04 concluídos com sucesso) |
| **Fluxo Principal** | 1. Agente confirma a escolha da ata<br>2. Sistema grava vínculo entre o processo (ETP de origem) e a ata selecionada<br>3. Sistema registra log estruturado da decisão (RQ-INT-05) |
| **Pós-condição** | Ata vinculada ao processo, pronta para envio ao G4 |

---

## UC06 — Exportar Resultado para G4 (TR)

| Campo | Descrição |
|---|---|
| **Ator** | Sistema (automático) |
| **Pré-condição** | UC05 concluído |
| **Fluxo Principal** | 1. Sistema monta pacote de dados conforme contrato de dados<br>2. Sistema envia/disponibiliza pacote para o módulo G4 |
| **Pós-condição** | G4 recebe dados da ata para elaboração do Termo de Referência |
