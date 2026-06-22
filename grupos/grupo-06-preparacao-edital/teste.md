# Plano de Testes — Módulo: Preparação do Edital e Envio à Prefeitura (G06)

## Regras de Negócio Consideradas

- **RN-ED-01:** O edital só pode ser formatado se o TR (G04) e o Mapa de Risco (G05) estiverem disponíveis e completos.
- **RN-ED-02:** O edital deve conter, obrigatoriamente, os anexos TR e Mapa de Risco para ser considerado completo.
- **RN-ED-03:** Um edital com pendências (anexos faltantes, campos obrigatórios vazios) não pode ser enviado à Prefeitura.
- **RN-ED-04:** Após o envio, o edital passa para status `ENVIADO` e se torna imutável.
- **RN-ED-05:** Todo envio deve registrar data/hora, responsável pelo envio e status de confirmação de recebimento pela Prefeitura.
- **RN-ED-06:** Um novo envio (reenvio) só é permitido se o status atual for `DEVOLVIDO` (Prefeitura solicitou correção).

---

## Casos de Teste — Recebimento de TR e Mapa de Risco

| ID | Cenário | Resultado Esperado |
|----|---------|---------------------|
| T-01 | Receber TR (G04) e Mapa de Risco (G05) completos e aprovados | ✅ Sistema vincula ambos os documentos ao edital em criação, status `EM_FORMATACAO` |
| T-02 | Receber apenas o TR, sem o Mapa de Risco | ❌ Sistema bloqueia início da formatação — exibe pendência "Mapa de Risco não recebido" (RN-ED-01) |
| T-03 | Receber TR com status diferente de "Aprovado" (ex.: "Em Revisão") | ❌ Sistema rejeita o vínculo — exibe mensagem "TR ainda não aprovado pelo G04" (RN-ED-01) |

---

## Casos de Teste — Formatação do Edital

| ID | Cenário | Resultado Esperado |
|----|---------|---------------------|
| T-04 | Formatar edital com TR e Mapa de Risco válidos, preenchendo campos obrigatórios (objeto, modalidade, prazos) | ✅ Sistema gera `EditalFormatado` no padrão oficial, status `FORMATADO` |
| T-05 | Formatar edital sem informar a modalidade de licitação (campo obrigatório) | ❌ Sistema rejeita — exibe mensagem "Campo 'Modalidade' é obrigatório" |
| T-06 | Formatar edital cujo TR e Mapa de Risco pertencem a processos diferentes (IDs divergentes) | ❌ Sistema bloqueia — exibe mensagem "TR e Mapa de Risco não pertencem ao mesmo processo" |

---

## Casos de Teste — Validação de Completude

| ID | Cenário | Resultado Esperado |
|----|---------|---------------------|
| T-07 | Validar edital formatado com TR e Mapa de Risco anexados e todos os campos preenchidos | ✅ Sistema marca edital como `VALIDADO`, libera para envio |
| T-08 | Validar edital sem o anexo do Mapa de Risco | ❌ Sistema bloqueia validação — exibe lista de pendências "Anexo 'Mapa de Risco' ausente" (RN-ED-02) |
| T-09 | Validar edital sem o anexo do TR | ❌ Sistema bloqueia validação — exibe lista de pendências "Anexo 'TR' ausente" (RN-ED-02) |
| T-10 | Validar edital com campos obrigatórios em branco (ex.: data de abertura do pregão) | ❌ Sistema bloqueia validação — exibe lista consolidada de pendências |

---

## Casos de Teste — Envio para Prefeitura

| ID | Cenário | Resultado Esperado |
|----|---------|---------------------|
| T-11 | Enviar edital com status `VALIDADO` para a Prefeitura | ✅ Sistema envia edital com anexos (TR + Mapa de Risco), status muda para `ENVIADO` (RN-ED-03/04) |
| T-12 | Tentar enviar edital com status `EM_FORMATACAO` (não validado) | ❌ Sistema bloqueia envio — exibe mensagem "Edital deve ser validado antes do envio" (RN-ED-03) |
| T-13 | Tentar enviar edital que já está com status `ENVIADO` | ❌ Sistema bloqueia — exibe mensagem "Edital já enviado em [data/hora]" (RN-ED-04) |
| T-14 | Reenviar edital com status `DEVOLVIDO` após correções | ✅ Sistema permite novo envio, gera novo registro de envio vinculado ao histórico (RN-ED-06) |

---

## Casos de Teste — Registro de Auditoria

| ID | Cenário | Resultado Esperado |
|----|---------|---------------------|
| T-15 | Após envio bem-sucedido, consultar histórico do edital | ✅ Sistema exibe usuário responsável, data/hora do envio e versão do edital enviado (RN-ED-05) |
| T-16 | Receber confirmação de recebimento da Prefeitura | ✅ Sistema atualiza status de envio para "Confirmado" e registra data/hora da confirmação (RN-ED-05) |
| T-17 | Consultar edital sem nenhuma tentativa de envio | ✅ Sistema exibe histórico vazio com indicação "Nenhum envio registrado" |

---

## Casos de Teste — Imutabilidade e Status

| ID | Cenário | Resultado Esperado |
|----|---------|---------------------|
| T-18 | Tentar editar TR ou Mapa de Risco vinculados a um edital com status `ENVIADO` | ❌ Sistema bloqueia edição — exibe mensagem "Edital enviado é imutável" (RN-ED-04) |
| T-19 | Tentar excluir anexo de um edital com status `VALIDADO` ou `ENVIADO` | ❌ Sistema bloqueia exclusão — exibe mensagem "Anexos não podem ser removidos após validação" |

---

## Resumo

| Categoria | Casos |
|-----------|-------|
| Recebimento de TR e Mapa de Risco | 3 |
| Formatação do Edital | 3 |
| Validação de Completude | 4 |
| Envio para Prefeitura | 4 |
| Registro de Auditoria | 3 |
| Imutabilidade e Status | 2 |
| **Total** | **19** |