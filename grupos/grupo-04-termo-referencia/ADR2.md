# Número do ADR: 002
## Título: Estratégia de Versionamento do TR e Parametrização da Lei 14.133/2021 (Regras de ME/EPP)
*Data: 2026-06-11* *Responsável: Grupo 04 — Módulo Termo de Referência* Status: Aceito

### Contexto
O sistema precisa aplicar regras baseadas na Lei Complementar nº 123/2006 (em conformidade com o Art. 4º da Lei nº 14.133/2021), que atualmente estipula o teto de cota exclusiva integral para itens de até R$ 80.000,00 e reserva de 25% para itens acima desse valor. No entanto, o cenário normativo de licitações públicas é mutável. Tetos de valores monetários e percentuais de cotas podem ser alterados ou reajustados por decretos e atualizações legislativas futuras. Fixar esses valores de forma estática comprometeria a longevidade do software.

### Decisão
Optou-se por não fixar (*hardcode*) os valores de "R$ 80.000,00" e "25%" diretamente no código-fonte da aplicação. A classe controladora `RegrasMEEPP` buscará esses parâmetros dinamicamente no banco de dados, onde estarão salvos e indexados em uma tabela de controle de "Versão da Lei". Além disso, a entidade `TermoReferencia` salvará internamente o ID de qual versão da lei estava em vigor e foi utilizada no momento exato da sua consolidação técnica.

### Justificativa
A decisão de usar uma arquitetura orientada a parâmetros dinâmicos baseou-se nas seguintes razões:

- **Evolução sem Impacto (Manutenibilidade):** Permite que o sistema se adapte a novas realidades financeiras e jurídicas sem exigir manutenção de código, novos testes ou paragens no ambiente.
- **Imutabilidade de Documentos Históricos:** Um TR finalizado em 2026 não pode recalcular os seus valores retroativamente se a lei mudar em 2028. Salvar o ID da versão garante que a memória de cálculo original permaneça intacta para fins de fiscalização.
- **Independência de Infraestrutura:** Isola as regras fiscais de validação do motor principal de software, facilitando futuras auditorias.

### Alternativas Consideradas
Foram consideradas as seguintes alternativas:

- **Hardcoding no Código (Constantes em Java):** Definir `private static final double LIMITE_LEI = 80000.00;`. Rejeitado, pois qualquer alteração na lei exigiria alteração no código, novos testes unitários, nova compilação e um novo deploy no servidor da FACAPE.
- **Arquivo de Configuração Externo (.properties / .env):** Salvar os valores em um arquivo de propriedades. Embora evite a recompilação, dificulta o rastreamento histórico de qual TR usou qual regra se as propriedades mudarem ao longo do tempo.

### Consequências
A escolha da parametrização dinâmica traz as seguintes consequências:

- **Agilidade na Manutenção:** Se o governo alterar o teto para R$ 100.000,00 amanhã, um usuário administrador apenas atualiza o registro no banco de dados. O sistema passa a aplicar o novo teto instantaneamente para os novos TRs.
- **Garantia de Observabilidade e Auditoria:** Facilita a integração com o módulo centralizado do **Grupo 09 (Auditoria, Compliance e Indicadores)**, pois o payload enviado para a trilha imutável conterá com precisão qual versão da lei validou aquela despesa pública.
- **Complexidade Adicional:** Exige o tratamento de exceções para cenários onde a consulta de parâmetros no banco de dados falhar, impedindo o cálculo se a tabela de parâmetros estiver inacessível (cenário mapeado no plano de testes).

### Referências
- **Lei nº 14.133/2021 (Nova Lei de Licitações):** Princípios de eficiência, eficácia e segregação de funções no processo administrativo.
- **Requisitos de Sistema (RQ-INT-02 e RQ-INT-05):** Diretrizes de rastreabilidade ponta a ponta e logs estruturados de transições de estados.