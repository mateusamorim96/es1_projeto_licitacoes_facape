# G3 — Gestão de Atas SRP
## Projeto AV2 — Engenharia de Software I — FACAPE 2026

---

## Descrição do Módulo

O módulo **G3 — Gestão de Atas SRP** é responsável por pesquisar Atas de Registro de Preço (ARP) vigentes que atendam à demanda descrita no ETP (recebido do G2) e calcular os limites de uso de cada ata, enviando o resultado ao módulo G4 (Termo de Referência).

- **Entrada:** ETP produzido pelo Grupo 2
- **Saída:** Informações de atas vigentes/compatíveis enviadas ao Grupo 4

---

## Estrutura do Repositório

```
G3-Gestao-Atas-SRP/
├── README.md                        ← Este arquivo
├── docs/
│   ├── Grupo3_Gestao_Atas_SRP.md   ← Documento completo do módulo
│   ├── casos-de-uso/
│   │   └── casos-de-uso.md          ← UC01 a UC06 detalhados
│   ├── diagramas/
│   │   ├── modelo-dominio.md        ← Diagrama UML de entidades
│   │   ├── sequencia.md             ← Diagrama de sequência
│   │   └── bpmn.md                  ← Fluxo BPMN
│   ├── adr/
│   │   └── adr.md                   ← ADR-01, ADR-02, ADR-03
│   └── contratos/
│       └── contrato-dados.md        ← Contrato de entrada/saída (RQ-INT-01)
├── backlog/
│   └── historias-usuario.md         ← US01 a US06 com critérios de aceite
├── testes/
│   └── plano-testes.md              ← Testes unitários e de integração
├── audit/
│   └── logs-auditoria.md            ← Eventos de log (RQ-INT-05) e rastreabilidade
└── src/
    ├── domain/
    │   └── models.md                ← Descrição das entidades do domínio
    ├── services/
    │   └── services.md              ← Lógica de negócio dos serviços
    └── controllers/
        └── controllers.md           ← Endpoints e controllers
```

---

## Requisitos Mínimos Atendidos

| Requisito | Descrição | Status |
|---|---|---|
| RQ-INT-01 | Contrato de entrada/saída documentado | ✅ `docs/contratos/` |
| RQ-INT-02 | Rastreabilidade ponta a ponta | ✅ `audit/` |
| RQ-INT-03 | Fundamento normativo registrado (RN-04, RN-08, RN-10) | ✅ `docs/casos-de-uso/` |
| RQ-INT-04 | Plano de testes + integração com G2 e G4 | ✅ `testes/` |
| RQ-INT-05 | Logs estruturados de transições de estado | ✅ `audit/` |

---

## Integrações

| Módulo | Tipo | Descrição |
|---|---|---|
| **G2 — ETP** | Entrada | Fornece o ETP com item/categoria/unidade/quantidade |
| **G4 — Termo de Referência** | Saída | Recebe ata selecionada para elaboração do TR |
| **G8 — Ordem de Fornecimento** | Dependência | Mantém dados de empenhos usados no cálculo de saldo |
| **G9 — Auditoria/BI** | Log | Consome eventos estruturados para trilha imutável |

---

*Grupo 3 — FACAPE 2026*
