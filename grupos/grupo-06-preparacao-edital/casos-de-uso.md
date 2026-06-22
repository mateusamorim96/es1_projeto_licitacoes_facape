# Casos de Uso - Grupo 06
## Preparação para Edital e Envio à Prefeitura

---

# UC-01 – Formatar Edital

## Descrição
Permite gerar um edital oficial a partir do Termo de Referência (TR) e do Mapa de Riscos recebidos de outros módulos.

## Atores
- Servidor Responsável

## Pré-condições
- TR aprovado e disponível.
- Mapa de Riscos aprovado e disponível.

## Fluxo Principal
1. O servidor acessa a funcionalidade de geração de edital.
2. O sistema recupera o TR e o Mapa de Riscos.
3. O sistema aplica o modelo oficial de edital.
4. O sistema gera o edital formatado.
5. O sistema salva o documento.

## Pós-condições
- Edital formatado disponível para validação.

---

# UC-02 – Validar Edital

## Descrição
Permite verificar se o edital possui todas as informações e anexos obrigatórios.

## Atores
- Servidor Responsável

## Pré-condições
- Edital formatado existente.

## Fluxo Principal
1. O servidor solicita a validação do edital.
2. O sistema verifica campos obrigatórios.
3. O sistema verifica anexos obrigatórios.
4. O sistema apresenta o resultado da validação.
5. O edital recebe status "Validado".

## Fluxo Alternativo
### 2A - Dados incompletos
1. O sistema identifica informações ausentes.
2. O sistema apresenta lista de pendências.
3. O edital permanece com status "Pendente".

## Pós-condições
- Edital validado ou pendente de correção.

---

# UC-03 – Enviar Edital para Prefeitura

## Descrição
Permite encaminhar o edital validado para a Prefeitura conduzir o pregão eletrônico.

## Atores
- Servidor Responsável
- Prefeitura

## Pré-condições
- Edital validado.

## Fluxo Principal
1. O servidor seleciona o edital.
2. O servidor informa o destinatário.
3. O sistema envia o edital.
4. O sistema recebe confirmação de envio.
5. O sistema atualiza o status para "Enviado".

## Fluxo Alternativo
### 4A - Falha no envio
1. O sistema identifica falha na comunicação.
2. O sistema registra o erro.
3. O edital permanece aguardando novo envio.

## Pós-condições
- Edital enviado ou aguardando reenvio.

---

# UC-04 – Registrar Tramitação Externa

## Descrição
Permite registrar informações de auditoria referentes ao envio do edital.

## Atores
- Sistema

## Pré-condições
- Envio realizado.

## Fluxo Principal
1. O sistema registra data e hora do envio.
2. O sistema registra usuário responsável.
3. O sistema registra destinatário.
4. O sistema registra versão do edital.
5. O sistema registra confirmação de recebimento.
6. O sistema armazena os dados para auditoria.

## Pós-condições
- Histórico de tramitação disponível para consulta.

---

# UC-05 – Consultar Histórico de Envios

## Descrição
Permite consultar todos os envios realizados para a Prefeitura.

## Atores
- Servidor Responsável
- Auditor

## Pré-condições
- Existirem registros de envio.

## Fluxo Principal
1. O usuário acessa a consulta.
2. O sistema exibe os registros.
3. O usuário filtra por período, responsável ou status.
4. O sistema apresenta os resultados encontrados.

## Pós-condições
- Histórico consultado.
