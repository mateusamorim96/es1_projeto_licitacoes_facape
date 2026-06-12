## 📋 Backlog do Produto (Módulo DFD)

### **US01: Cadastro de Demanda pelas Secretarias**
> **Como** Representante da Secretaria,  
> **Eu quero** preencher e enviar um formulário com a demanda de materiais ou serviços da minha pasta,  
> **Para que** as necessidades da minha secretaria sejam incluídas no planejamento de compras da prefeitura.

*   **Critérios de Aceite:**
    *   O sistema deve apresentar um formulário com os campos: Tipo de Item, Quantidade, Justificativa e Secretaria.
    *   O sistema não deve permitir o envio do formulário se campos obrigatórios estiverem em branco.
    *   Após o envio, o status da demanda deve constar como "Aguardando Validação".
    *   O usuário deve receber uma confirmação visual e um número de protocolo do registro.

---

### **US02: Normalização e Validação de Itens**
> **Como** Analista de Compras,  
> **Eu quero** visualizar, revisar e editar as descrições dos itens solicitados,  
> **Para que** eu possa padronizar as especificações técnicas e evitar compras com descrições incorretas ou ambíguas.

*   **Critérios de Aceite:**
    *   O Analista deve poder acessar uma lista com todas as demandas no status "Aguardando Validação".
    *   O sistema deve permitir a edição do campo "Descrição do Item" pelo Analista, mantendo o histórico da descrição original enviada pela secretaria.
    *   O Analista deve poder aprovar a demanda (mudando o status para "Validada") ou devolvê-la para a secretaria solicitando correções.

---

### **US03: Identificação e Agrupamento de Duplicatas**
> **Como** Analista de Compras,  
> **Eu quero** que o sistema identifique ou me permita mesclar itens idênticos solicitados por secretarias diferentes,  
> **Para que** eu possa consolidar as quantidades em um único lote de compra, ganhando escala.

*   **Critérios de Aceite:**
    *   O sistema deve sugerir possíveis duplicatas com base na similaridade textual das descrições dos itens validados.
    *   O Analista deve ter a opção de selecionar duas ou mais demandas e utilizar a função "Mesclar Itens".
    *   Ao mesclar, o sistema deve somar as quantidades solicitadas, preservando o registro de qual proporção pertence a cada secretaria de origem.

---

### **US04: Consolidação das Demandas**
> **Como** Analista de Compras,  
> **Eu quero** agrupar todas as demandas validadas e mescladas em uma lista unificada,  
> **Para que** eu possa fechar o pacote de necessidades para um determinado período ou processo licitatório.

*   **Critérios de Aceite:**
    *   O sistema deve permitir a seleção em massa de itens "Validados" para inclusão em um pacote de consolidação.
    *   Deve ser possível remover um item do pacote antes do fechamento final.
    *   Ao concluir, o status do pacote consolidador deve mudar para "Consolidado", travando a edição das quantidades.

---

### **US05: Geração do DFD (Documento de Formalização da Demanda)**
> **Como** Analista de Compras,  
> **Eu quero** gerar e exportar o DFD a partir das demandas consolidadas,  
> **Para que** eu possa formalizar a etapa inicial e enviar as informações para o módulo de Estudo Técnico Preliminar (G02).

*   **Critérios de Aceite:**
    *   O sistema deve possuir um botão "Gerar DFD" associado a um pacote de demandas consolidado.
    *   O documento gerado deve conter o cabeçalho padrão da prefeitura, a lista unificada de itens, as quantidades totais, a origem (secretarias) e as justificativas consolidadas.
    *   O documento deve ser exportável (ex.: PDF e disponibilizado via integração estruturada para o módulo G02).

---

### **US06: Trilha de Auditoria (Logs)**
> **Como** Auditor / Gestor do Sistema,  
> **Eu quero** consultar o histórico de ações realizadas em cada demanda,  
> **Para que** eu possa identificar quem criou, alterou ou validou um item e garantir a transparência do processo.

*   **Critérios de Aceite:**
    *   Toda criação, edição, mesclagem ou mudança de status de uma demanda deve gravar um registro automático no banco de dados.
    *   O log deve conter: Data/Hora, Usuário, Ação Realizada, Valor Anterior e Valor Atual.
    *   Na tela de detalhes de cada demanda, deve haver uma aba "Histórico" exibindo essa trilha de forma imutável para o usuário comum.