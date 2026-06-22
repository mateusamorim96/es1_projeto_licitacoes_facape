# Grupo 06 — Preparação para Edital e Envio à Prefeitura

## Módulo do Sistema

Formatação do TR e Mapa de Risco em documento oficial (edital) e envio para a Prefeitura conduzir o pregão eletrônico.

## Responsabilidade

- Receber TR (G04) e Mapa de Risco (G05)
- Formatar edital em padrão oficial
- Anexar TR e Mapa de Risco
- Validar completude do edital
- Enviar para Prefeitura (que conduzirá o pregão eletrônico)
- Registrar data/hora/confirmação de envio

**Entradas:** TR (G04), Mapa de Risco (G05)  
**Saídas:** Edital formatado e enviado para a Prefeitura

---

## Entregas Mínimas

| Artefato | Descrição |
|----------|-----------|
| Casos de uso (mínimo 4) | Formatar edital, validar, enviar para Prefeitura, registrar envio |
| Diagrama UML de classes | `Edital`, `EditalFormatado`, `Anexo`, `TramiteExterno`, `Destinatario` |
| Diagrama de sequência | Formatação → validação → envio |
| BPMN | Fluxo de tramitação interna e externa |
| Backlog | Mínimo 5 histórias de usuário |
| ADRs (mínimo 2) | Ex.: qual formato oficial? PDF assinado digitalmente? |
| Testes | Validação de formato, completude de anexos |
| Auditoria | Quem enviou, quando, qual versão, confirmação de recebimento |

---

## Interfaces com Outros Módulos

- **Entrada ← G04 (TR):** TR
- **Entrada ← G05 (Mapa de Riscos):** Mapa de Riscos
- **Saída → G07 (Acompanhamento Externo):** Edital enviado

---

## Entrega do Grupo

> Preencha esta seção ao finalizar:

- **Integrantes: Enzo Gabriel Lima Nunes, João Victor da Silva Costa Vasconcelos, Victor Emmanuel Silva Ramos, Juliardo Mateus Brito de Sa Junior, Gustavo de Souza Mendonça**
- **Data de entrega: 12/06/2026**
- **Branch/PR: Preparação edital **

---

## 📋 O que entregar

### Artefato 1: `epicos.md`
Lista dos épicos com descrição, objetivos e prioridade.

### Artefato 2: `user-stories.md`
Todas as user stories, organizadas por épico, com:
- ID único
- Título
- Narrativa: "Como [ator], quero [ação], para [valor]"
- Critérios de aceite (Given/When/Then)
- Estimativa de complexidade (S/M/L/XL)
- Prioridade (Alta/Média/Baixa)

### Artefato 3: `backlog-priorizado.md`
Backlog na ordem de implementação sugerida (produto mínimo viável primeiro).

---

## 🗂️ Épicos Sugeridos

| ID | Épico | Descrição |
|----|-------|-----------|
| EP-01 | Gestão de Demandas | Coleta e consolidação de necessidades de todas as secretarias |
| EP-02 | Plano de Contratação Anual | Elaboração e publicação do PCA |
| EP-03 | Elaboração de Documentos | ETP, Mapa de Riscos e Termo de Referência |
| EP-04 | Pesquisa de Preços | Integração com Banco de Preços e cotação manual |
| EP-05 | Gestão de Atas SRP | Controle de atas vigentes, saldos e vencimentos |
| EP-06 | Ordens de Fornecimento | Emissão, rastreamento e notificação de OF |
| EP-07 | Integração Externa | PNCP e sistema 1doc da Prefeitura |
| EP-08 | Notificações e Alertas | Prazos, vencimentos e pendências |

---

## 📄 Exemplos de User Stories

### EP-01: Gestão de Demandas

```
US-01: Registrar demanda de material
Como Secretaria/Colegiado
Quero registrar uma demanda de materiais ou serviços no sistema
Para que minha necessidade seja incluída no processo licitatório do ano

Critérios de Aceite:
  Cenário 1: Registro bem-sucedido
    Dado que estou autenticada como representante de secretaria
    E o período de coleta de demandas está aberto
    Quando preencho: descrição, justificativa, itens com catmat e quantidade
    E submeto o formulário
    Então o sistema registra a demanda com status "Aguardando Consolidação"
    E notifica o Almoxarifado sobre nova demanda

  Cenário 2: Período de coleta fechado
    Dado que o período de coleta de demandas está encerrado
    Quando tento registrar uma demanda
    Então o sistema exibe mensagem: "Período de coleta encerrado. Contate o Almoxarifado."
    E não permite o registro

Estimativa: M | Prioridade: Alta | Épico: EP-01
```

```
US-04: Verificar fracionamento de despesa
Como Almoxarifado
Quero que o sistema me alerte quando houver risco de fracionamento de despesa
Para que eu não abra dois processos do mesmo tipo no mesmo exercício

Critérios de Aceite:
  Cenário 1: Fracionamento detectado
    Dado que já existe um DFD aberto para "Material de Expediente" em 2026
    Quando uma secretaria registra nova demanda de "Material de Expediente"
    Então o sistema exibe alerta: "Já existe DFD aberto para esta categoria. A demanda será incluída no processo vigente."
    E adiciona automaticamente a demanda ao DFD existente

Estimativa: M | Prioridade: Alta | Épico: EP-01
```

---

## 🛠️ Ferramentas Recomendadas

| Ferramenta | Link | Observação |
|-----------|------|------------|
| **Markdown + VS Code** | local | Simples e versionável — **recomendado** |
| **Trello** | [trello.com](https://trello.com) | Visualização de backlog em kanban |
| **GitHub Projects** | No próprio repo | Integra com o repositório |
| **Notion** | [notion.so](https://notion.so) | Bom para tabelas e templates |

---

## 📋 Checklist de Qualidade

- [x] Toda US tem ator, ação e valor claramente expressos
- [x] Critérios de aceite cobrem o caminho feliz e pelo menos um fluxo alternativo
- [x] Regras de negócio (RN-XX) estão referenciadas nas USs relevantes
- [x] Backlog priorizado distingue MVP de funcionalidades futuras
- [x] Estimativas de complexidade são relativas entre si (não absolutas)

---

## 📁 Estrutura esperada da pasta

```
grupo-06-backlog/
├── README.md
├── epicos.md
├── user-stories.md
└── backlog-priorizado.md
```

---

## ✏️ Seção de Entrega (preencher pelo grupo)

**Integrantes:**
- Enzo Gabriel Lima Nunes, Victor Emmanuel Silva Ramos, João Victor da Silva Costa Vasconcelos, Gustavo de Souza Mendonça, Juliardo Mateus Brito de Sá Junior.

**Decisões tomadas:**
ADR-01: Modelamos o fluxo em duas pools — "Módulo G06" (tramitação interna) e "Prefeitura" (tramitação externa) — para separar claramente o que está sob controle do sistema do que depende de um agente externo.

ADR-02: A validação de completude do edital foi modelada como gateway de decisão exclusivo: se houver pendências (TR, Mapa de Risco ou campos obrigatórios faltando), o fluxo retorna automaticamente para a etapa de correção, impedindo o envio de um edital incompleto.

ADR-03: O envio do edital para a Prefeitura foi representado como fluxo de mensagem entre as duas pools (e não como continuação direta do processo), por se tratar de uma comunicação entre sistemas/organizações distintos. Caso a Prefeitura devolva o edital, esse retorno reutiliza o mesmo ciclo de correção interno.

ADR-04: Incluímos uma tarefa dedicada de registro de data/hora/status logo após o envio, para atender ao requisito de auditoria. O fim da tramitação interna do G06 foi definido nesse ponto — o acompanhamento posterior é responsabilidade do G07.

**Limitações identificadas:**
A principal dificuldade foi delimitar até onde vai a responsabilidade do G06 e onde começa a do G07, já que o processo de envio/resposta da Prefeitura é, por natureza, externo ao nosso sistema. Também não foi simples decidir o nível de detalhe da pool "Prefeitura" — optamos por simplificá-la, sem representar suas etapas internas (protocolo, jurídico, etc.). O diagrama ainda não cobre aspectos como prazos/SLA, falhas técnicas no envio, versionamento do edital em caso de reenvio e limite de repetições do loop de correção, pontos que podem precisar ser revisitados em versões futuras do BPMN.