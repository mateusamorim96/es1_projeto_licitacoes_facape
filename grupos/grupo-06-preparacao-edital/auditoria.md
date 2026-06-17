# Plano de Auditoria e Rastreabilidade — Grupo 06

Este documento especifica os requisitos de auditoria e controle de integridade para as operações de consolidação e transmissão externa de editais.

## 🛡️ Definição da Trilha de Auditoria (Logs)

Para cada ação executada no módulo de preparação e envio de edital, o sistema persistirá, de forma imutável no banco de dados, os seguintes metadados:

| Evento | Quem Executou | Dados Capturados | Confirmação / Retorno |
| :--- | :--- | :--- | :--- |
| **Geração da Minuta** | ID do Operador de Compras | ID do TR (G04), ID do Mapa (G05), Versão do Edital (ex: v1.0) | Status: "Em Elaboração" |
| **Alteração de Conteúdo** | ID do Operador de Compras | Campos modificados, Justificativa da mudança | Versão incrementada (ex: v1.1) |
| **Validação de Compliance**| ID do Validador/Sistema | Checklist de campos obrigatórios checados | Status: "Apto" ou "Pendente" |
| **Assinatura Digital** | CPF do Diretor (via Gov.br) | Provedor da Assinatura, Hash SHA-256 do arquivo PDF | Assinatura ICP-Brasil embutida |
| **Envio para a Prefeitura** | ID do Usuário Transmissor | Payload enviado, IP da máquina de origem, Data/Hora UTC | **Código de Protocolo da Prefeitura** |

---

## 🔒 Mecanismo de Integridade (Hash SHA-256)

Para garantir que o edital enviado à Prefeitura seja exatamente o mesmo que o módulo de Acompanhamento Externo (G07) vai monitorar, o sistema implementará a checagem de "impressão digital eletrônica":

1. No momento do envio bem-sucedido, o sistema calcula o código hash criptográfico do arquivo PDF.
2. Esse hash é armazenado no log de auditoria local.
3. Ao disponibilizar a saída para o módulo **G07**, o sistema envia o PDF acompanhado do seu Hash correspondente.
4. Se qualquer caractere ou vírgula do edital for modificado por terceiros fora do sistema, o Hash mudará, disparando um alerta de segurança por violação de integridade.

---

## 📋 Exemplo de Registro de Log (Representação JSON)

Em nível de implementação de banco de dados, cada transação de auditoria gerará um registro estruturado como o exemplo abaixo:

```json
{
  "log_id": "LOG-2026-98745",
  "timestamp": "2026-06-12T18:43:00Z",
  "usuario": {
    "id": "USR-4412",
    "nome": "Enzo Gabriel Lima Nunes",
    "cargo": "Presidente da Comissão de Licitação"
  },
  "acao": "TRANSMISSAO_EXTERNA_EDITAL",
  "detalhes": {
    "processo_numero": "FACAPE-2026.0089",
    "documento_versao": "2.0_FINAL_ASSINADO",
    "arquivo_hash": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855"
  },
  "retorno_externo": {
    "sistema_destinatario": "Prefeitura_1Doc_API",
    "status_code": 201,
    "codigo_protocolo_recebimento": "PRM-2026-ABC-991"
  }
}