# ADR-005 — Estratégia de Notificações

**Data:** 2026-04-22  
**Status:** Aceita  
**Autores:** Grupo 07

---

## Contexto

O módulo G07 deve notificar os usuários relevantes quando houver mudança de status no pregão. Com base no fluxo descrito em aula, hoje a FACAPE já comunica:

- Fiscais, Gestores e Contabilidade sobre contratos/atas **via e-mail ou WhatsApp**;
- Controle de vencimento de contratos **por e-mail periódico**.

Os prazos do processo licitatório são críticos: um pregão adjudicado precisa ser comunicado rapidamente ao G08 para que a Ordem de Fornecimento seja emitida. Mudanças de status como *fracassado* ou *deserto* também exigem ação rápida do setor de licitações.

---

## Decisão

Adotamos **notificações por e-mail via SMTP** como canal primário, combinadas com **notificações in-app** (banner/badge na interface do sistema). O WhatsApp **não** será implementado diretamente pelo módulo por questões de custo e complexidade da API oficial.

---

## Opções Consideradas

### Opção A: E-mail via SMTP + Notificações In-App
- **Descrição:** Ao detectar mudança de status, o sistema envia e-mail para os usuários cadastrados no papel relevante e exibe uma notificação na interface web do sistema.
- **Prós:** E-mail é o canal já usado pela FACAPE; SMTP é simples e gratuito (ex.: Gmail SMTP, SMTP institucional); in-app garante visibilidade mesmo sem abrir e-mail.
- **Contras:** E-mails podem cair em spam; não há garantia de leitura imediata; in-app requer que o usuário esteja logado.

### Opção B: WhatsApp Business API
- **Descrição:** Envio de mensagens automáticas via WhatsApp Business API quando o status muda.
- **Prós:** Canal já usado informalmente pela FACAPE; alta taxa de leitura imediata.
- **Contras:** Custo por mensagem (Meta cobra por conversa iniciada); exige aprovação de templates de mensagem pela Meta; LGPD requer consentimento explícito para envio via WhatsApp; complexidade de implementação e manutenção.

### Opção C: Somente Notificações In-App (fluxo pull)
- **Descrição:** Nenhuma notificação proativa; os usuários verificam o sistema quando quiserem.
- **Prós:** Sem dependência de canal externo; implementação mínima.
- **Contras:** Prazos críticos podem ser perdidos se ninguém verificar o sistema a tempo; contradiz a necessidade de agilidade no processo licitatório.

### Opção D: Push Notifications (PWA/mobile)
- **Descrição:** Notificações push via Service Worker para usuários que acessam pelo celular.
- **Prós:** Notificação imediata sem abrir o app; funciona mesmo com tela bloqueada.
- **Contras:** Requer implementação de PWA completa; usuários precisam conceder permissão; complexidade extra para o escopo acadêmico.

---

## Critérios de Decisão

| Critério | Peso | E-mail + In-App | WhatsApp API | Somente In-App | Push/PWA |
|----------|------|----------------|-------------|----------------|----------|
| Aderência ao uso atual da FACAPE | Alto | ✅ | ✅ | ❌ | ⚠️ |
| Custo de implementação | Alto | ✅ | ❌ | ✅ | ⚠️ |
| Garantia de entrega em prazos críticos | Alto | ⚠️ | ✅ | ❌ | ✅ |
| Conformidade com LGPD | Alto | ✅ | ⚠️ | ✅ | ✅ |
| Complexidade de implementação | Médio | ✅ | ❌ | ✅ | ❌ |

---

## Consequências

### Positivas
- Alinhamento com o fluxo já existente na FACAPE (e-mail para fiscais/gestores/contabilidade);
- Notificação in-app garante que usuários logados vejam mudanças imediatamente;
- SMTP pode usar o servidor institucional da FACAPE ou serviço gratuito (ex.: Mailtrap em dev, Gmail em produção).

### Negativas / Riscos
- Não há garantia de leitura imediata do e-mail fora do horário de trabalho;
- Lista de destinatários precisa ser mantida atualizada no sistema.

### Neutras / Trade-offs
- A arquitetura de notificações será desacoplada via um módulo interno de eventos, permitindo adicionar novos canais (WhatsApp, push) no futuro sem alterar a lógica de negócio.

---

## Conformidade com a Lei 14.133/2021

As notificações de mudança de status garantem que os responsáveis sejam comunicados tempestivamente sobre adjudicação, homologação ou fracasso do pregão, permitindo ação nos prazos legais previstos na lei (ex.: prazo para recurso, prazo para assinatura do contrato).

---

*Referências:*  
- [Lei nº 14.133/2021, Art. 90 (prazos de adjudicação e homologação)](https://www.planalto.gov.br/ccivil_03/_ato2019-2022/2021/lei/l14133.htm)  
- [LGPD — Lei nº 13.709/2018](https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm)  
- Nodemailer (SMTP para Node.js): https://nodemailer.com
