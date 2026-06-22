### 1 Compilar ETP e Ata em Rascunho do TR 
Descrição: O sistema busca os dados dos módulos anteriores para montar a base do Termo de Referência sem digitação manual. 
Ator Principal: Especialista em Licitações 
Pré-condições: Deve existir um ETP aprovado (Módulo G02) e uma Decisão de Ata SRP consolidada (Módulo G03). 
Fluxo Principal: 
O usuário acessa a tela de geração de TR e seleciona o ETP aprovado. 
O sistema consome as APIs dos módulos G02 e G03. 
O sistema extrai campos essenciais (objeto, justificativa, quantidades, número da ata). 
O sistema gera automaticamente um rascunho do TR na tela.  
Fluxos de Exceção: Se os módulos G02 ou G03 estiverem fora do ar, o sistema exibe um alerta de erro de comunicação e orienta o usuário a tentar novamente. 

### 2 Calcular e Aplicar Cota Exclusiva (Itens até R$ 80.000,00) 
Descrição: Garantir que compras de baixo valor sejam destinadas exclusivamente para micro e pequenas empresas (ME/EPP). 
Ator Principal: Sistema (processo automatizado) 
Pré-condições: O rascunho do TR deve ter os itens e valores estimados carregados. 
Fluxo Principal: 
O sistema multiplica a quantidade do item pelo valor unitário estimado. 
O sistema identifica que o valor total do lote é menor ou igual a R$ 80.000,00. 
O sistema classifica 100% da quantidade daquele item na categoria CotaExclusiva. 
O sistema trava o campo de edição, impedindo que o usuário remova a exclusividade sem justificativa. 
Pós-condição: O item fica garantido para ME/EPP em conformidade com a Lei 14.133/2021. 

### 3 Dividir Cotas Proporcionais (Itens acima de R$ 80.000,00) 
Descrição: Separar itens de alto valor em duas fatias: uma exclusiva para pequenos negócios e outra de ampla concorrência. 
Ator Principal: Sistema (processo automatizado) 
Pré-condições: O valor total do item/lote deve ser estritamente maior que R$ 80.000,00. 
Fluxo Principal: 
O sistema detecta o valor superior ao teto legal. 
O sistema divide a quantidade física do item: 25% são alocados para a CotaExclusiva e 75% para a CotaAberta. 
Se a divisão de 25% gerar um número quebrado (fração), o sistema arredonda para cima em favor da CotaExclusiva. 
O sistema injeta no documento a cláusula de "Empate Ficto" (margem de 10% de vantagem). 
Pós-condição: O lote fica dividido fisicamente e contabilmente no documento do TR. 

### 4 Validar e Gerar TR Final 
Descrição: Revisão final do documento, preenchimento de dados de entrega e envio oficial para os próximos setores. 
Ator Principal: Chefe de Compras 
Pré-condições: Rascunho do TR gerado e cotas calculadas corretamente pelo sistemaz 
Fluxo Principal:  
O usuário preenche os campos obrigatórios restantes (Local de Entrega, Prazo e Critérios de Aceitação). 
O usuário clica em "Validar e Finalizar TR". 
O sistema roda uma auditoria interna para confirmar se a Lei 14.133/2021 foi aplicada corretamente. 
O sistema registra a data, hora, ID do usuário e a versão da lei (Trilha de Auditoria). 
O sistema atualiza o status para "TR Formatado e Validado". 
O sistema dispara o documento finalizado para o Módulo G05 (Mapa de Riscos) e G06 (Edital). 
Fluxos de Exceção: Se o usuário tentar finalizar sem preencher os Prazos ou o Local de Entrega, o sistema bloqueia a ação e destaca os campos obrigatórios em vermelho. 