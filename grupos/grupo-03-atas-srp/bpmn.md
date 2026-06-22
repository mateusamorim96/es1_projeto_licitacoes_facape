# Diagrama BPMN — G3 Gestão de Atas SRP

## Fluxo Principal do Processo

```
[Início]
   │
   ▼
(Receber ETP do G2)
   │
   ▼
<Pesquisar atas vigentes compatíveis>
   │
   ├── Nenhuma ata encontrada ──► (Registrar "sem ata disponível") ──► [Fim — segue para nova licitação]
   │
   ▼ (Atas encontradas)
<Selecionar ata candidata>
   │
   ▼
<Calcular saldo disponível (RN-04)>
   │
   ▼
◇ Ata é de terceiro (carona)?
   │                         │
  Sim                        Não
   │                         │
   ▼                         │
<Calcular limite de carona (RN-08)>     │
   │                         │
   ▼                         │
◇ Dentro do limite (50%)?    │
   │           │              │
  Sim          Não            │
   │           │              │
   │           ▼              │
   │   (Bloquear / alertar) ──┘──► volta para "Selecionar outra ata candidata"
   │
   ▼
<Registrar ata selecionada (UC05)>
   │
   ▼
<Gerar log estruturado (RQ-INT-05)>
   │
   ▼
<Enviar dados para G4 (UC06)>
   │
   ▼
[Fim]
```

## Raias (Swim Lanes)

| Raia | Atividades |
|---|---|
| **Agente de Compras** | Recebe ETP, seleciona ata candidata, confirma ata, decide sobre carona |
| **Sistema G3** | Pesquisa atas, filtra compatíveis, calcula saldo, calcula carona, registra vínculo, gera log, envia para G4 |
| **Base de Atas** | Fornece lista de atas vigentes e dados de empenho |
| **G2 (ETP)** | Fornece dados de entrada |
| **G4 (TR)** | Recebe pacote de saída |
| **G9 (Auditoria)** | Consome logs estruturados |

## Eventos de Negócio

| Evento | Gatilho | Destino |
|---|---|---|
| ETP recebido | G2 envia ETP validado | Início do UC01 |
| Ata selecionada | Agente confirma escolha | UC05 + log |
| Sem ata disponível | Busca retorna vazia | Log + notificação para nova licitação |
| Carona bloqueada | Quantidade > 50% limite | Log + alerta ao agente |
| Resultado enviado ao G4 | UC06 concluído | Fim do processo G3 |
