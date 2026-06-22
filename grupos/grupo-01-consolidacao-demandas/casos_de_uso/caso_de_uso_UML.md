@startuml diagrama-casos-de-uso
left to right direction
skinparam packageStyle rectangle

' --- Atores do Sistema ---
actor "Secretaria / Colegiado" as SEC
actor "Almoxarifado" as ALM
actor "Chefe de Compras" as CC
actor "Assessoria Jurídica" as JUR

' --- Sistemas Externos ---
actor "PNCP" as PNCP <<Sistema>>
actor "Banco de Preços Gov" as BP <<Sistema>>
actor "Sistema da Prefeitura (1Doc)" as PREF <<Sistema>>

' --- Fronteira do Sistema ---
rectangle "Sistema de Gestão de Licitações - FACAPE" {
  
  ' Bloco de Demandas e Consolidação
  usecase "UC04: Registrar Demanda de Material/Serviço" as UC04
  usecase "UC13: Acompanhar Status da Demanda" as UC13
  usecase "UC02: Consolidar DFD" as UC02
  usecase "UC05: Elaborar Plano de Contratação Anual (PCA)" as UC05
  
  ' Bloco de Estudos e Artefatos do Planejamento
  usecase "UC07: Elaborar Estudo Técnico Preliminar (ETP)" as UC07
  usecase "UC08: Elaborar Mapa de Riscos" as UC08
  usecase "UC09: Elaborar Termo de Referência (TR)" as UC09
  
  ' Bloco de Pesquisa e Cotações
  usecase "UC06: Realizar Cotação de Preços" as UC06
  usecase "UC14: Consultar Banco de Preços" as UC14
  usecase "UC03: Consultar PNCP" as UC03
  
  ' Bloco Jurídico e Integração Municipal
  usecase "UC01: Validar Documentação Jurídica" as UC01
  usecase "UC10: Enviar Processo à Prefeitura" as UC10
  
  ' Bloco de Execução e Fornecedores
  usecase "UC11: Gerenciar Atas de Registro de Preços" as UC11
  usecase "UC12: Emitir Ordem de Fornecimento" as UC12
  usecase "UC15: Notificar Prazo de Entrega" as UC15
}

' --- Ligações dos Atores ---
SEC --> UC04
SEC --> UC13

ALM --> UC02

CC --> UC05
CC --> UC07
CC --> UC08
CC --> UC09
CC --> UC06
CC --> UC10
CC --> UC11
CC --> UC12

JUR --> UC01

' --- Regras de Inclusão e Extensão ---
UC02 ..> UC04 : <<include>>
UC09 ..> UC07 : <<include>>
UC06 ..> UC14 : <<include>>
UC06 ..> UC03 : <<include>>
UC12 ..> UC15 : <<include>>

' --- Integrações de Sistemas ---
UC03 --> PNCP
UC05 --> PNCP
UC14 --> BP
UC10 --> PREF

@enduml