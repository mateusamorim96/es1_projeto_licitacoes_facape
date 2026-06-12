# ADR-002 — Estratégia de Integração com o PNCP

**Data:** 2026-04-22  
**Status:** Aceita  
**Autores:** Grupo 07

---

## Contexto

O módulo G07 precisa acompanhar o pregão eletrônico publicado no Portal Nacional de Contratações Públicas (PNCP), que é a plataforma oficial determinada pela Lei nº 14.133/2021 para publicação de editais (fase externa). O sistema deve:

- Detectar mudanças de status do pregão (aberto → em julgamento → adjudicado/fracassado/deserto);
- Registrar empresa vencedora, valor final e datas relevantes;
- Alimentar o módulo G08 com o resultado do pregão.

O PNCP disponibiliza uma API REST pública. A plataforma da Prefeitura também pode ter portal próprio, eventualmente integrado ao PNCP.

---

## Decisão

Adotamos **polling periódico via API REST do PNCP** como estratégia de integração. O sistema consultará a API em intervalos configuráveis (ex.: a cada 30 minutos durante dias úteis) e comparará o status retornado com o último estado registrado, gerando eventos internos quando houver mudança.

---

## Opções Consideradas

### Opção A: Polling Periódico (API REST)
- **Descrição:** Um job agendado (ex.: cron ou scheduler interno) consulta a API do PNCP em intervalos regulares, verifica se o status mudou e atualiza o banco local.
- **Prós:** Simples de implementar; funciona independentemente de suporte a webhooks pelo PNCP; totalmente controlável pela equipe.
- **Contras:** Pequeno delay entre a mudança real e a detecção; gera requisições mesmo quando nada mudou.

### Opção B: Webhook (Push da plataforma)
- **Descrição:** A plataforma da Prefeitura/PNCP notifica ativamente o sistema G07 quando há mudança de status.
- **Prós:** Atualização em tempo real; sem requisições desnecessárias.
- **Contras:** O PNCP não oferece webhooks públicos documentados para terceiros; depende de configuração na plataforma da Prefeitura, que pode não ser viável academicamente.

### Opção C: Integração Manual Assistida
- **Descrição:** Um operador humano consulta a plataforma e insere os dados manualmente no sistema G07.
- **Prós:** Sem dependência de API; funciona mesmo com indisponibilidade do portal.
- **Contras:** Propenso a erros humanos; não escala; não atende ao requisito de auditoria automatizada; contradiz a proposta do módulo.

---

## Critérios de Decisão

| Critério | Peso | Polling Periódico | Webhook | Manual |
|----------|------|------------------|---------|--------|
| Viabilidade técnica atual | Alto | ✅ | ⚠️ | ✅ |
| Automatização do acompanhamento | Alto | ✅ | ✅ | ❌ |
| Custo de implementação | Médio | ✅ | ⚠️ | ✅ |
| Latência de detecção | Baixo | ⚠️ | ✅ | ❌ |
| Independência de terceiros | Alto | ✅ | ❌ | ✅ |

---

## Consequências

### Positivas
- Implementação viável dentro do prazo acadêmico;
- Auditoria automática de cada consulta (data, hora, resultado retornado);
- Independência em relação à disponibilidade de webhooks no PNCP.

### Negativas / Riscos
- Pequena janela de atraso entre a mudança real e a detecção (aceitável para o contexto);
- Possibilidade de rate limiting pela API do PNCP se o intervalo for muito pequeno.

### Neutras / Trade-offs
- O intervalo de polling deve ser configurável para balancear atualidade e carga na API externa.

---

## Conformidade com a Lei 14.133/2021

A integração via API do PNCP está alinhada à exigência da lei de publicação e acompanhamento dos pregões em portal oficial. O registro de cada consulta garante rastreabilidade do acompanhamento, conforme princípios de transparência da lei.

---

*Referências:*  
- [API PNCP — Documentação oficial](https://www.gov.br/pncp/pt-br/acesso-a-informacao/sistemas/pncp-api)  
- [Lei nº 14.133/2021, Art. 54](https://www.planalto.gov.br/ccivil_03/_ato2019-2022/2021/lei/l14133.htm)
