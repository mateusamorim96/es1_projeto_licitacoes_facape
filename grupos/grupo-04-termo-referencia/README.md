# Grupo 04 — Elaboração de Termo de Referência (TR)
 **Integrantes:**
 - Astrid Rodrigues Ribeiro,
 - Israela Silva Ferreira,
 - Maria Rita Rodrigues de Souza,
 - Rayssa Mel Nascimento de Carvalho,
 - Rayssa Rodrigues Sousa,

---
 - Data de entrega: 12/06/2026
 - ⁠Branch/PR: feature/grupo-04/termo-de-referencia
---


**Decisões tomadas:**
> ADR-01 (Motor de Cálculo): Opção pelo uso estrito de Ceil em vez de arredondamento padrão (Round half up) ou para baixo (Floor). Isso garante que o piso de fomento legal de 25% nunca seja violado (ex: em 9 itens, 25% é 2,25; o arredondamento padrão reduziria para 2 itens, violando o mínimo legal).

> ADR-02 (Parametrização Dinâmica): Proibição de valores fixos (hardcode) no código Java para o teto de R$ 80.000,00 e o percentual de 25%. Os dados são buscados dinamicamente no banco de dados com base em uma tabela versionada. Cada TR gerado salva o ID da versão legislativa vigente, garantindo que alterações futuras por decretos não alterem retroativamente a memória de cálculo de documentos antigos (Auditoria e Imutabilidade).

**Limitações identificadas:**
> Complexidade Interdisciplinar: Desafio no refinamento conceitual inicial para transpor as nuances jurídicas da Lei nº 14.133/2021 em regras lógicas e restrições de código determinísticas.

> Delimitação de Fronteiras de Integração: Dificuldade preliminar no isolamento estrito do escopo do módulo frente aos fluxos macro da instituição.

> Abstração de Cenários de Exceção: Alta complexidade na modelagem de fluxos alternativos para o tratamento de lotes compostos por bens unitários indivisíveis ou de alto valor agregado.
