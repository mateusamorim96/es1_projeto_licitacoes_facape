@startuml diagrama-sequencia-identico
' --- Configurações Visuais para Casar com o Print (Estilo Flat/Clean) ---
skinparam style strictuml
skinparam shadowlighting false
skinparam defaultFontName "Arial"
skinparam SequenceMessageAlignment center

' Cores do padrão do seu print (Preto, Branco e Tons de Cinza)
skinparam ActorBackgroundColor #FFFFFF
skinparam ActorBorderColor #000000
skinparam ParticipantBackgroundColor #FFFFFF
skinparam ParticipantBorderColor #000000
skinparam NoteBackgroundColor #FFFFFF
skinparam NoteBorderColor #000000

' --- Elementos do Fluxo ---
actor "Secretaria" as SEC
actor "Almoxarifado" as ALM
participant "Interface UI" as UI
participant "GerenciadorDemandas" as CTRL
database "Banco de Dados" as DB
database "Banco de Auditoria" as AUD

== FASE 01: Coleta de Demandas ==

SEC -> UI : Registrar nova demanda (Item, Qtd, Justificativa)
UI -> CTRL : salvarDemanda(dados)

' Auditoria da Interação da Secretaria
CTRL ->> AUD : salvarLogAuditoria(user="Secretaria", acao="REGISTRAR_DEMANDA", dados)

CTRL -> DB : INSERT INTO TB_DEMANDA (status = 'EM_ANALISE')
DB --> CTRL : Confirmado
CTRL --> UI : Retorna objeto criado
UI --> SEC : Exibe: "Demanda salva com sucesso!"

== FASE 02: Triagem Técnica e Higienização ==

ALM -> UI : Listar demandas pendentes
UI -> CTRL : listarPorStatus('EM_ANALISE')

' Auditoria da Interação de Leitura do Almoxarifado
CTRL ->> AUD : salvarLogAuditoria(user="Almoxarifado", acao="CONSULTAR_DEMANDAS_PENDENTES")

CTRL -> DB : SELECT * FROM TB_DEMANDA WHERE status = 'EM_ANALISE'
DB --> CTRL : Lista de registros
CTRL --> UI : Retorna Array de demandas
UI --> ALM : Exibe tabela na tela

ALM -> UI : Validar especificação e vincular CATMAT
UI -> CTRL : validarDemanda(id, codigoCATMAT)

' Auditoria da Interação de Validação do Almoxarifado
CTRL ->> AUD : salvarLogAuditoria(user="Almoxarifado", acao="VALIDAR_DEMANDA", id)

CTRL -> DB : UPDATE TB_DEMANDA SET status = 'VALIDADA'
DB --> CTRL : Confirmado
CTRL --> UI : Sucesso
UI --> ALM : Atualiza grid da tela

== FASE 03: Consolidação e Geração do DFD ==

ALM -> UI : Acionar comando "Consolidar Período"
UI -> CTRL : processarConsolidacao()

' Auditoria da Interação de Consolidação do Almoxarifado
CTRL ->> AUD : salvarLogAuditoria(user="Almoxarifado", acao="CONSOLIDAR_PERIODO")

note over CTRL : Agrupa demandas com mesmo CATMAT\ne soma as quantidades (Evita fracionamento)

CTRL -> DB : SELECT SUM(qtd) FROM TB_DEMANDA WHERE status = 'VALIDADA' GROUP BY catmat
DB --> CTRL : Dados agrupados por item
CTRL -> DB : INSERT INTO TB_DFD (status = 'EMITIDO')
CTRL -> DB : UPDATE TB_DEMANDA SET status = 'CONSOLIDADA'
DB --> CTRL : Operações confirmadas
CTRL --> UI : Retorna ID do DFD gerado
UI --> ALM : Disponibiliza download do DFD (PDF/JSON)

@enduml