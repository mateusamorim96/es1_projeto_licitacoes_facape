# Backlog Priorizado — Grupo 06: Preparação de Edital e Envio à Prefeitura

Este documento apresenta o backlog priorizado do módulo de Preparação de Edital e Envio à Prefeitura, estruturado de modo a focar no **Produto Mínimo Viável (MVP)** primeiro, garantindo a entrega do fluxo de valor central antes das demais funcionalidades.

---

## 🎯 Estratégia de Priorização

A priorização foi definida com base nas dependências de processo e no valor gerado para a instituição:
1. **Foco no Fluxo de Valor Principal (MVP):** Criação, montagem, validação e envio do edital são fundamentais para que o processo licitatório siga para a fase externa na Prefeitura.
2. **Mitigação de Riscos de Integração:** O envio via 1doc (integração externa) possui maior complexidade e foi priorizado para as sprints iniciais de desenvolvimento, evitando gargalos de última hora.
3. **Auditoria e Compliance:** Embora sejam requisitos essenciais da disciplina e da Lei 14.133, a implementação detalhada da auditoria e relatórios é colocada em uma fase imediatamente posterior à estabilização do fluxo transacional básico.

---

## 🗓️ Divisão em Sprints / Releases (MVP vs. Post-MVP)

### 🚀 MVP (Produto Mínimo Viável) - Release 1.0
O MVP garante o fluxo fim a fim: receber a documentação de entrada (TR e Mapa de Riscos), gerar o edital oficial de acordo com as regras legais, validar a completude e realizar a transmissão via 1doc para a Prefeitura de Petrolina.

### 📈 Post-MVP - Release 1.1
Melhorias de usabilidade, auditoria completa integrada ao módulo central (G09) e logs finos de rastreabilidade.

---

## 📋 Tabela do Backlog Priorizado

| Ordem | ID | Título | Épico | Prioridade | Estimativa | Justificativa de Posicionamento |
|:---:|:---:|:---|:---|:---:|:---:|:---|
| **1** | [US-01](user-stories.md#us-01---gerar-edital-automaticamente) | Gerar edital automaticamente | EP-01 | Alta | M | **Fundação do Processo**: Primeiro passo obrigatório. Sem gerar o edital a partir do TR e Mapa de Riscos, não há o que validar ou enviar. Aplica a **RN-07** na data do pregão. |
| **2** | [US-03](user-stories.md#us-03---anexar-e-organizar-documentos-obrigatorios) | Anexar e organizar documentos obrigatórios | EP-02 | Alta | M | **Consistência Documental**: Garante que os artefatos gerados pelos grupos anteriores (TR do G04 e Mapa de Riscos do G05) sejam anexados ao edital. |
| **3** | [US-02](user-stories.md#us-02---visualizar-edital-formatado) | Visualizar edital formatado | EP-01 | Alta | S | **Prevenção de Erros**: Permite a revisão visual humana rápida (em PDF) do edital antes que ele seja submetido a validações e envio. Baixa complexidade (S). |
| **4** | [US-04](user-stories.md#us-04---validar-documentos-obrigatorios-e-consistencia-do-edital) | Validar documentos e consistência | EP-03 | Alta | M | **Portão de Qualidade**: Garante a conformidade das regras de negócio e a presença de todas as partes obrigatórias, impedindo que editais com pendências ou fora dos prazos cheguem ao envio. |
| **5** | [US-05](user-stories.md#us-05---enviar-edital-para-prefeitura-via-1doc) | Enviar edital para Prefeitura via 1doc | EP-04 | Alta | L | **Sucesso da Fase Interna**: Conexão com o sistema externo. É o objetivo final do módulo. Por envolver integração de API e alta complexidade (L), deve ser iniciada assim que a base de dados estiver pronta. |
| **6** | [US-06](user-stories.md#us-06---registrar-historico-de-envio-e-acoes-do-edital) | Registrar histórico de envio e ações (Auditoria) | EP-05 | Média | S | **Transparência e Rastreabilidade**: Registra quem enviou, quando e qual versão, atendendo aos critérios de compliance (integração com G09). Fica no final do backlog pois não impede a execução do fluxo básico, embora seja obrigatório para conformidade final. |

---

## 🚦 Critérios de Pronto (Definition of Done - DoD)

Para que qualquer história de usuário seja dada como concluída, ela deve cumprir os seguintes critérios:
- [ ] Código revisado por outro membro do grupo.
- [ ] Testes unitários cobrindo o caminho feliz e os fluxos alternativos implementados e passando.
- [ ] Interface visual aderente aos padrões de acessibilidade e design do sistema.
- [ ] Logs estruturados implementados e mapeados para integração com o módulo G09 (Auditoria).
- [ ] Documentação de API / Contrato de Integração atualizada na pasta docs (se aplicável).
<<<<<<< HEAD
=======
.
>>>>>>> e65149434e40ecbf5cf8ae6f2992a67b7132243d
