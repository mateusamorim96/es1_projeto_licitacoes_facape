# Plano de Testes — RQ-INT-04
## G3 Gestão de Atas SRP

---

## Testes Unitários

| ID | Caso de Teste | Entrada | Resultado Esperado |
|---|---|---|---|
| TU01 | Calcular saldo com consumo parcial | `quantidadeRegistrada=100`, `empenhado=30` | `saldo=70` |
| TU02 | Calcular saldo com consumo zero (RN-04) | `quantidadeRegistrada=100`, `empenhado=0` | `saldo=100`, sem erro/alerta |
| TU03 | Calcular limite de carona | `quantidadeRegistrada=200` | `limiteCarona=100` |
| TU04 | Bloquear carona acima do limite (RN-08) | `limiteCarona=100`, `jaAderido=80`, `desejado=30` | `bloqueado=true` (80+30 > 100) |
| TU05 | Filtrar atas expiradas | ata com `dataValidade < hoje` | ata não aparece na lista de UC01 |
| TU06 | Compatibilidade de item — categoria diferente | `categoriaETP="Informática"`, `categoriaAta="Mobiliário"` | `compatível=false` |
| TU07 | Compatibilidade de item — categoria e unidade iguais | `categoriaETP="TI"`, `categoriaAta="TI"`, `unidade="UN"` | `compatível=true` (depende da descrição) |
| TU08 | Saldo não negativo | `quantidadeRegistrada=50`, `empenhado=60` | `saldo=0` (ou erro de consistência) |

---

## Testes de Integração (com G2 e G4 — RQ-INT-04: "2 vizinhos")

| ID | Caso de Teste | Descrição | Resultado Esperado |
|---|---|---|---|
| TI01 | Recebimento de ETP do G2 | G2 envia ETP no formato do contrato | G3 processa sem erro e inicia UC01 |
| TI02 | Envio de resultado ao G4 — ata encontrada | G3 envia pacote com `ataEncontrada=true` | G4 recebe e confirma (ack) |
| TI03 | Envio de resultado ao G4 — ata não encontrada | G3 envia pacote com `ataEncontrada=false` | G4 recebe `observacoes` e trata o caso de "sem ata" |
| TI04 | Formato de ETP inválido (campo faltando) | G2 envia ETP sem `itemSolicitado.categoria` | G3 rejeita com erro estruturado, registra log (RQ-INT-05) |

---

## Rastreabilidade Testes → Histórias

| Teste | US relacionada | Regra de negócio |
|---|---|---|
| TU01, TU02, TU08 | US02 | RN-04 |
| TU03, TU04 | US03 | RN-08 |
| TU05 | US01 | Vigência de ata |
| TU06, TU07 | US01 | UC02 — compatibilidade |
| TI01 | US01 | RQ-INT-01 |
| TI02, TI03 | US05 | RQ-INT-01 |
| TI04 | US06 | RQ-INT-05 |

---

## Evidências a Entregar

- [ ] Resultados dos testes unitários (TU01–TU08)
- [ ] Mock do G2 enviando ETP (para TI01, TI04)
- [ ] Mock do G4 recebendo pacote e confirmando (para TI02, TI03)
- [ ] Logs gerados nos testes de integração (RQ-INT-05)
