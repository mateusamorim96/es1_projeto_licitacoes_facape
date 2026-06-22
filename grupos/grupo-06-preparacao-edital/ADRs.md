# ADR-01 — Formato Oficial do Edital

**Status:** Aprovado

## Contexto
O sistema precisa gerar um edital oficial a partir das informações recebidas do Termo de Referência (TR) e do Mapa de Riscos. Esse documento será enviado à Prefeitura para condução do pregão eletrônico.

É necessário definir um formato que preserve a integridade do documento e seja amplamente aceito pelos órgãos públicos.

## Decisão
O edital será gerado no formato **PDF**.

## Justificativa
- Mantém a formatação independente do dispositivo utilizado.
- É amplamente utilizado em processos administrativos e licitatórios.
- Facilita o arquivamento e a auditoria.
- Evita alterações acidentais após a geração do documento.
- Permite futura aplicação de assinatura digital.

## Consequências

### Positivas
- Maior confiabilidade do documento.
- Compatibilidade com sistemas governamentais.
- Facilidade de compartilhamento e armazenamento.

### Negativas
- Edição posterior do documento torna-se mais difícil.
- Necessidade de ferramenta para conversão do conteúdo para PDF.

---

# ADR-02 — Registro de Envio do Edital

**Status:** Aprovado

## Contexto
Após a geração do edital, é necessário registrar o envio para a Prefeitura, garantindo rastreabilidade e suporte à auditoria.

O sistema deve permitir comprovar quando e por quem o documento foi enviado.

## Decisão
Todo envio de edital será registrado em banco de dados contendo:

- Data e hora do envio;
- Usuário responsável;
- Destinatário;
- Número da versão do edital;
- Status do envio;
- Confirmação de recebimento.

## Justificativa
- Atende requisitos de auditoria.
- Permite rastrear falhas e reenviar documentos quando necessário.
- Garante histórico completo das tramitações externas.
- Facilita consultas futuras.

## Consequências

### Positivas
- Maior controle sobre os envios.
- Histórico completo das operações.
- Apoio a processos de auditoria e fiscalização.

### Negativas
- Necessidade de armazenamento adicional.
- Aumento da complexidade do módulo de envio.
