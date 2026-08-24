## 📦 PR do Grupo: [G04 — Elaboração do Termo de Referência]

**Integrantes:**
- Astrid Rodrigues Ribeiro 26384
- Israela Silva Ferreira 26144
- Maria Rita Rodrigues de Souza 28596
- Rayssa Mel Nascimento de Carvalho 26338
- Rayssa Rodrigues Sousa 26220
---

## 📋 Artefatos Entregues

| Arquivo | Descrição |
|---------|-----------|
| grupos/grupo-04-termo-de-referencia/README.md | README do grupo contendo os integrantes, decisões tomadas e limitações do projeto. |
| grupos/grupo-04-termo-de-referencia/HistoriasUsuario.md | Backlog com 5 Histórias de Usuário (US01 a US05) detalhadas com critérios de aceite focados em automação e regras de negócio. |
| grupos/grupo-04-termo-de-referencia/ADR1.md | ADR 001: Arquitetura do Motor de Cálculo de Cotas e aplicação estrita do arredondamento para cima (Ceil). |
| grupos/grupo-04-termo-de-referencia/ADR2.md | ADR 002: Estratégia de Versionamento do TR e Parametrização Dinâmica da Lei nº 14.133/2021 em Banco de Dados. |
| grupos/grupo-04-termo-de-referencia/Testes.md | Plano de testes contendo 15 casos de teste abrangendo cálculos de cotas, entradas inválidas e integrações de fronteira. |
| grupos/grupo-04-termo-de-referencia/Auditoria.md | Diretrizes de compliance, observabilidade e definição do modelo de Payload JSON enviado para o barramento do Grupo 09. |

---

## 🎯 Decisões Tomadas

A primeira decisão crítica tomada pelo grupo diz respeito à criação do algoritmo para o Motor de Cálculo de Cotas (ADR-01). Em conformidade com as diretrizes da Lei Complementar nº 123/2006, os itens que superam o limite de R$ 80.000,00 devem ter uma reserva física de 25% destinada exclusivamente para Microempresas e Empresas de Pequeno Porte (ME/EPP). Para mitigar o problema de divisões que resultam em valores fracionados em bens que são essencialmente indivisíveis (por exemplo, obter 2,5 computadores), estabelecemos o uso estrito da função matemática `Ceil` (arredondamento para cima) sempre em benefício da cota protegida. Essa medida afasta decisões manuais arbitrárias e cumpre rigorosamente o piso legal determinado para o fomento de pequenos negócios.

A segunda decisão relevante foi a adoção da Parametrização Dinâmica da Lei (ADR-02). Vetamos o uso de valores fixos codificados diretamente no código (*hardcode*) para o teto de R$ 80.000,00 e o percentual de 25%. Essas variáveis operam de forma dinâmica a partir de consultas a tabelas indexadas no banco de dados. Essa escolha garante a manutenibilidade do sistema perante mudanças na legislação e preserva de forma fidedigna a memória de cálculo histórica de cada Termo de Referência já homologado.

---

## ⚠️ Limitações e Itens em Aberto

* **Complexidade Interdisciplinar:** O principal obstáculo encontrado pelo grupo foi realizar o alinhamento conceitual e transpor os termos jurídicos e nuances da Lei nº 14.133/2021 e da LC nº 123/2006 para regras de negócio puramente lógicas e determinísticas.
* **Delimitação de Fronteiras de Integração:** Tivemos dificuldades preliminares para isolar com exatidão onde terminava o escopo do nosso módulo e onde iniciavam os fluxos macros dos grupos subsequentes.
* **Abstração de Cenários de Exceção Complexos:** Verificou-se uma alta complexidade para mapear e modelar fluxos alternativos e planos de contingência voltados a lotes compostos por itens estritamente unitários e indivisíveis de elevado valor financeiro.

---

## 🔗 Rastreabilidade

- [X] Baseado no contexto do sistema
- [X] Baseado no glossário do domínio
- [X] Baseado na Lei 14.133/2021 (Art. 4º e regras de proteção e fomento à ME/EPP dispostas na Lei Complementar nº 123/2006)
- [X] Baseado em artefatos de outros grupos (especifique): Importa e consome dados do **Grupo 02 (ETP)** e do **Grupo 03 (Ata SRP)** para a montagem do TR; fornece o documento finalizado para as rotinas do **Grupo 05 (Mapa de Riscos)** e **Grupo 06 (Edital)**; transmite payloads estruturados de auditoria para o barramento do **Grupo 09 (Auditoria e Compliance)**.

---

## ✅ Checklist Obrigatório

- [X] Todos os arquivos estão dentro de grupos/grupo-04-termo-de-referencia/
- [X] Nenhum arquivo foi modificado fora da pasta do grupo
- [X] Exportações em imagem (PNG ou SVG) estão presentes
- [X] Fontes editoráveis estão presentes (.puml, .drawio, .bpmn, etc.)
- [X] O README.md do grupo está preenchido
- [X] Os commits seguem o padrão [G04] tipo: descrição
- [X] Todos os membros do grupo contribuíram com commits

---

## 📸 Screenshots (se aplicável)
