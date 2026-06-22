# Backlog — Histórias de Usuário
## G3 Gestão de Atas SRP

---

## Histórias de Usuário

| ID | História | Critérios de Aceite |
|---|---|---|
| US01 | Como Agente de Compras, quero pesquisar atas vigentes compatíveis com o item do ETP, para evitar abrir uma nova licitação desnecessária. | - Sistema retorna apenas atas com status "vigente"<br>- Sistema filtra por categoria/unidade de medida/descrição |
| US02 | Como Agente de Compras, quero ver o saldo disponível de uma ata, para saber se ela atende minha demanda. | - Sistema exibe quantidade registrada, empenhada e saldo<br>- Cálculo segue RN-04 (sem mínimo obrigatório) |
| US03 | Como Agente de Compras, quero saber o limite de carona de uma ata de terceiro, para não violar a legislação. | - Sistema calcula 50% da quantidade original (RN-08)<br>- Sistema bloqueia seleção se quantidade desejada exceder o limite |
| US04 | Como Agente de Compras, quero registrar a ata escolhida vinculada ao processo, para manter rastreabilidade ponta a ponta (RQ-INT-02). | - Vínculo gravado com referência ao ETP de origem<br>- Log estruturado gerado |
| US05 | Como Sistema, quero enviar os dados da ata selecionada para o módulo G4, para que o Termo de Referência seja elaborado corretamente. | - Pacote de dados segue contrato definido em `docs/contratos/`<br>- Confirmação de recebimento registrada |
| US06 | Como Agente de Compras, quero ser avisado quando nenhuma ata compatível existir, para seguir o fluxo de nova licitação com cotação de 3 fornecedores (RN-10). | - Mensagem clara exibida<br>- Estado registrado para auditoria (G9) |

---

## Priorização (MoSCoW)

| ID | Prioridade | Justificativa |
|---|---|---|
| US01 | Must Have | Função principal do módulo |
| US02 | Must Have | Necessário para validar viabilidade da ata |
| US03 | Must Have | Exigência legal (RN-08) |
| US04 | Must Have | Rastreabilidade obrigatória (RQ-INT-02) |
| US05 | Must Have | Integração obrigatória com G4 (RQ-INT-01) |
| US06 | Should Have | Garante continuidade do fluxo quando sem ata |

---

## Mapeamento US → UC

| US | UC relacionado |
|---|---|
| US01 | UC01, UC02 |
| US02 | UC03 |
| US03 | UC04 |
| US04 | UC05 |
| US05 | UC06 |
| US06 | Fluxo alternativo de UC01 |
