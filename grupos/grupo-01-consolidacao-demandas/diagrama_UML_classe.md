@startuml
skinparam classAttributeIconSize 0

enum StatusDemanda {
  EM_ANALISE
  VALIDADA
  RECUSADA
  CONSOLIDADA
}

enum StatusDFD {
  RASCUNHO
  EMITIDO
  ARQUIVADO
}

class Secretaria {
  - id: Long
  - nome: String
  - centroCusto: String
  + obterDemandas(): List<Demanda>
}

class Demanda {
  - id: Long
  - tipo: String
  - quantidade: Integer
  - justificativa: String
  - status: StatusDemanda
  - criadoPor: String
  - dataCriacao: LocalDateTime
  + alterarStatus(novoStatus: StatusDemanda)
}

class Item {
  - id: Long
  - codigoCATMAT: String
  - descricaoCompleta: String
  - unidadeMedida: String
  - precoUnitarioEstimado: Double
}

class DFD {
  - id: Long
  - codigoProtocolo: String
  - dataConsolidacao: LocalDateTime
  - status: StatusDFD
  - valorTotalEstimado: Double
  +Exportarmetadados() Json
  +ExportarParaBanco() SQL
}

Secretaria "1" -- "N" Demanda : registra >
Demanda "N" -- "N" Item : especifica >
DFD "1" -- "N" Item : consolida >
Demanda ..> StatusDemanda : usa >
DFD ..> StatusDFD : usa >
@enduml