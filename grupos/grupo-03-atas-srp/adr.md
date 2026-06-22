# ADRs — Architecture Decision Records
## G3 Gestão de Atas SRP

---

## ADR-01 — Critério de Compatibilidade de Item

- **Status:** Aceito
- **Data:** 2026
- **Contexto:** É preciso decidir como o sistema vai considerar um item da ata "compatível" com o item do ETP.
- **Decisão:** Compatibilidade = mesma categoria + mesma unidade de medida + similaridade textual mínima na descrição.
- **Alternativas consideradas:** Comparação apenas por categoria (rejeitada — gera muitos falsos positivos).
- **Consequências:** Pode haver itens compatíveis "na prática" que o sistema não identifique por diferenças de descrição; revisão manual pelo agente continua necessária.

---

## ADR-02 — Cálculo de Saldo em Tempo de Consulta (não armazenado)

- **Status:** Aceito
- **Data:** 2026
- **Contexto:** O saldo de uma ata muda conforme novos empenhos (OFs) são registrados pelo G8.
- **Decisão:** O saldo é **calculado dinamicamente** (`quantidade_registrada − soma de empenhos`), nunca armazenado como valor fixo.
- **Alternativas consideradas:** Armazenar saldo e atualizar via evento (rejeitada — risco de inconsistência se um evento do G8 falhar).
- **Consequências:** Consulta de saldo depende de o G8 manter os dados de empenho atualizados; módulo G3 não tem "fonte da verdade" própria sobre consumo.

---

## ADR-03 — Tratamento de "Nenhuma Ata Encontrada"

- **Status:** Aceito
- **Data:** 2026
- **Contexto:** O fluxo precisa de um caminho claro quando não há ata compatível.
- **Decisão:** Sistema gera registro explícito de "sem ata disponível" e direciona para o fluxo de nova licitação (que exigirá cotação de 3 fornecedores, RN-10), sem bloquear o processo.
- **Consequências:** G9 (Auditoria) recebe esse evento para fins de indicadores (ex.: % de demandas atendidas por ata existente vs. nova licitação).

---

## Referências Normativas

| Código | Regra | Impacto nas decisões |
|---|---|---|
| RN-04 | Em contratos SRP, compra pode ser de 0% a 100% em 12 meses | ADR-02: saldo calculado dinamicamente sem mínimo obrigatório |
| RN-08 | Adesão por carona limitada a 50% do quantitativo original | ADR-01: verificação de compatibilidade antecede cálculo de carona |
| RN-10 | Cotação mínima de 3 fornecedores quando não há ata | ADR-03: redirecionamento explícito ao fluxo de nova licitação |
