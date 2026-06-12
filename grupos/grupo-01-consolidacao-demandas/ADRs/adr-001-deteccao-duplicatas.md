# ADR 001: Mecanismo para Detecção de Itens Duplicados na Consolidação

* **Data:** 02/06/2026
* **Status:** Aceito
* **Autores:** Grupo 01

## 1. Contexto
O Módulo G01 recebe formulários de demandas de diversas secretarias de forma descentralizada. É comum que secretarias descrevam o mesmo item de formas heterogêneas (ex: "Papel A4 Branco", "Papel Sulfite A4 75g", "Resma de Papel A4"). Se consolidarmos de forma puramente manual, o processo será lento e propenso a erros. Se automatizarmos 100%, corremos o risco de agrupar incorretamente itens que possuem especificações críticas diferentes (ex: medicamentos ou cartuchos de tinta específicos).

## 2. Decisão
Decidimos implementar uma **abordagem híbrida auxiliada por algoritmo**. O sistema utilizará uma combinação de correspondência textual aproximada (algoritmo de similaridade de strings como *Levenshtein Distance* ou *Dice's Coefficient*) combinado com filtros por categoria de item. 
O sistema agrupará e sugerirá os itens com índice de similaridade acima de 75% na tela do **Analista de Compras**. A ação definitiva de fusão e normalização textual (definir o nome padrão do item para o DFD) será feita obrigatoriamente por uma **homologação manual** do operador.

## 3. Alternativas Consideradas
* **Automação Total via IA/Regex:** Descartada devido ao risco legal de consolidar itens diferentes em licitações públicas, o que geraria falhas no fornecimento posterior.
* **Consolidação 100% Manual:** Descartada porque viola a eficiência do sistema, considerando que uma prefeitura pode gerar milhares de linhas de demandas anualmente.

## 4. Consequências
### ✅ Consequências Positivas (Ganhos)
* Redução drástica do tempo de triagem do Analista de Compras através de agrupamentos automáticos.
* Segurança jurídica e operacional, já que a palavra final da fusão de itens é de um humano responsável.
* Padronização gradativa do catálogo de materiais da prefeitura.

### ❌ Consequências Negativas (Custos/Riscos)
* Complexidade adicional de desenvolvimento no back-end para processar as strings e calcular os pesos de similaridade.
* A interface do usuário (UI) exigirá uma tela específica e mais complexa para o gerenciamento de conflitos de itens sugeridos.