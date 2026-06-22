@startuml bpmn-processo-dfd-com-auditoria
|Secretaria Requisitante|
|#LightBlue|Setor de Compras (Analista)|
|#LightGray|Módulo de Auditoria (Sistema)|

|Setor de Compras (Analista)|
start
:Estipular Prazo Limite de Coleta;
:Abrir Período de Recolha de Demandas;

|Secretaria Requisitante|
:Identificar necessidades da secretaria;
:Preencher Formulário de Demanda\n(Tipo, Qtd, Justificativa);

|Setor de Compras (Analista)|
if (Prazo de Coleta Expirado?) then (não)
    |Secretaria Requisitante|
    :Submeter Demanda no Sistema;
    
    |Módulo de Auditoria (Sistema)|
    :Registar Ação: CRIACAO_INICIAL;
    
    |Setor de Compras (Analista)|
    :Armazenar Demanda como "Pendente";
else (sim)
    |Setor de Compras (Analista)|
    :Bloquear Novos Registos no Sistema;
    
    |Módulo de Auditoria (Sistema)|
    :Registar Ação: BLOQUEIO_PRAZO;
endif

|Setor de Compras (Analista)|
:Reunir todas as demandas recebidas;
:Revisar Especificações Técnicas;

:Executar Algoritmo de Similaridade (ADR 001);
if (Itens Duplicados Detetados?) then (sim)
    :Agrupar Quantidades e \nNormalizar Nome do Item;
    
    |Módulo de Auditoria (Sistema)|
    :Registar Ação: ITEM_FUNDIDO e \nEDICAO_ESPECIFICACAO;
else (não)
endif

|Setor de Compras (Analista)|
:Gerar Documento de Formalização da Demanda (DFD);
:Validar e Assinar DFD Consolidado;

|Módulo de Auditoria (Sistema)|
:Registar Ação: GERACAO_DFD;

|Setor de Compras (Analista)|
:Disponibilizar DFD para o Módulo G02 (ETP);
end
@enduml