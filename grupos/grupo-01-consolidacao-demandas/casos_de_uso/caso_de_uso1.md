UC01: Cadastrar e Modificar Demanda de Material/Serviço
Ator Principal: Secretaria / Colegiado (Coordenador de Curso).

Atores Secundários: Nenhum.

Pré-condições: Usuário autenticado no sistema da FACAPE e período de planejamento orçamentário anual aberto pelo Gestor do PCA.

Fluxo Principal:

O Coordenador acessa o painel do Módulo 01 e clica em "Nova Demanda".

O sistema preenche automaticamente o ID da Secretaria e o Centro de Custo com base no perfil do usuário logado.

O Coordenador pesquisa e seleciona o item desejado utilizando a base de dados do CATMAT/CATSER.

O Coordenador insere a Quantidade desejada, o Valor Estimado Unitário e preenche a Justificativa Institucional/Pedagógica.

O Coordenador clica em "Submeter Demanda".

O sistema valida se todos os campos obrigatórios estão preenchidos e se o período de planejamento orçamentário continua aberto.

O sistema salva o documento, altera o status da demanda para SUBMETIDA e mantém a opção de edição visível para o autor.

Fluxos Alternativos / Exceções:

FA01 - Campo Vazio: Se a justificativa tiver menos de 30 caracteres ou a quantidade for igual/menor que zero, o sistema emite um alerta de erro e impede a submissão.

FA02 - Modificação de Demanda Submetida: Enquanto o período de planejamento orçamentário anual configurado pelo Gestor do PCA estiver aberto, o Coordenador pode selecionar uma demanda já submetida, alterar seus campos e salvá-la novamente. O sistema atualiza o registro e mantém o status como SUBMETIDA.

FA03 - Bloqueio por Encerramento de Período: Se o prazo do período de planejamento expirar ou for fechado pelo Gestor do PCA, o sistema altera internamente as permissões das demandas e bloqueia permanentemente o documento para novas edições por parte da Secretaria.

Pós-condições: A demanda é salva ou atualizada no banco de dados com o histórico de alterações, timestamp e ID do emissor registrados para fins de auditoria.

UC02: Validar, Filtrar e Especificar Itens da Demanda
Ator Principal: Almoxarifado (Responsável pelo almoxarifado).

Pré-condições: Usuário autenticado com perfil de Almoxarifado no sistema da FACAPE, período de planejamento encerrado e existência de demandas com o status SUBMETIDA.

Fluxo Principal:

O operador do Almoxarifado acessa o Módulo de Triagem.

O sistema solicita as credenciais de autenticação (usuário/senha ou certificado digital) e valida as permissões de acesso do operador.

O operador entra na fila de triagem de demandas do sistema.

O sistema exibe todas as demandas enviadas pelas secretarias que estão pendentes de análise.

O operador seleciona uma demanda específica para analisar as características e descrições dos itens solicitados.

O operador analisa a especificação detalhada fornecida pela secretaria e vincula o item ao código específico e correspondente do catálogo CATMAT/CATSER (ex: diferenciando o tipo exato de caneta, papel ou equipamento), garantindo que itens com propriedades distintas recebam códigos identificadores diferentes.

O operador realiza o cruzamento com o saldo do estoque físico atual da FACAPE para aquele modelo exato de item especificado.

O operador clica em "Validar Item" (o status da demanda muda para VALIDADA).

Fluxos Alternativos / Exceções:

FA01 - Falha na Autenticação: Se as credenciais do operador forem inválidas ou ele não possuir o perfil de acesso necessário, o sistema bloqueia o acesso à fila de triagem e exibe uma mensagem de erro.

FA02 - Item Disponível em Estoque (Cancelamento do requerimento): Se o Almoxarifado identificar que a faculdade já possui o material guardado naquela especificação exata e em quantidade suficiente, o operador clica em "Recusar Item", insere a justificativa e o status muda para RECUSADA.

FA03 - Especificação Incorreta ou Incongruente: Se a secretaria cadastrou uma descrição que não existe no catálogo ou misturou itens diferentes na mesma linha, o operador pode clicar em "Retornar para Ajuste", inserindo uma nota técnica para que a secretaria corrija a especificação.

Pós-condições: O item fica marcado como VALIDADA e ganha uma indexação por código de catálogo individualizado e imutável no banco de dados, separando-o de variações semelhantes para a etapa de consolidação, com o registro de auditoria indexando qual operador realizou a ação.

UC03: Consolidar Demandas por Código CATMAT e Chave Primária do Item
Ator Principal: Almoxarifado (Responsável pelo almoxarifado).

Pré-condições: Pelo menos duas demandas de secretarias diferentes com o status VALIDADA, devidamente indexadas por seus respectivos códigos específicos de catálogo e chaves primárias identificadoras únicas.

Fluxo Principal:

O operador do Almoxarifado acessa o painel de fechamento de lote do período.

O operador aciona a rotina "Executar Consolidação de Itens".

O sistema roda o motor de regras no backend, agrupando de forma automática todas as linhas de pedidos utilizando uma chave composta combinando a Chave Primária individual do item específico com o código numérico do CATMAT, atuando como mecanismo de desduplicação seguro e preciso.

O sistema soma os quantitativos e calcula o valor total acumulado do lote de compras de forma isolada para cada especificação única.

O sistema exibe na tela a listagem prévia unificada para revisão.

Fluxos Alternativos / Exceções:

FA01 - Alerta de Fracionamento (RN-03): Se o sistema detectar que itens idênticos foram deixados fora do grupo ou divididos em processos separados para burlar a lei, ele emite um aviso impeditivo em tela ao operador.

Pós-condições: O sistema gera um lote temporário indexado contendo o somatório exato e detalhado das demandas do período segregadas por sua chave primária correspondente.

UC04: Emitir Documento de Formalização da Demanda (DFD)
Ator Principal: Almoxarifado / Chefe de Compras.

Pré-condições: Rotina de consolidação (UC03) finalizada com sucesso.

Fluxo Principal:

O usuário visualiza o lote consolidado de itens aprovados.

O usuário clica no comando "Gerar e Emitir DFD Único".

O sistema gera a numeração de protocolo oficial imutável seguindo a máscara padrão (Ex: FACAPE-DFD-2026-0001).

O sistema aplica automaticamente a regra RN-01 / RN-02 (EXCLUSIVO_ME_EPP) caso o valor total estimado do lote de itens seja menor ou igual a R$ 80.000.

O sistema altera o status de todas as demandas originais das secretarias para CONSOLIDADA.

O sistema emite o documento oficial em formato estruturado (JSON e PDF).

Pós-condições: O DFD assume o status EMITIDO e o payload fica disponível via API para consumo do Módulo 02 (ETP/TR), travando qualquer possibilidade de alteração nos dados coletados.