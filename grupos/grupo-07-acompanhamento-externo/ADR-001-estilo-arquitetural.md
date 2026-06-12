# ADR-001 — Estilo Arquitetural

**Data:** 2026-04-22  
**Status:** Aceita  
**Autores:** Grupo 07

---

## Contexto

O módulo G07 é responsável pelo acompanhamento do pregão eletrônico na plataforma da Prefeitura, conforme exigências da Lei nº 14.133/2021. O sistema precisa:

- Consultar periodicamente a plataforma externa (Portal de Compras Públicas / PNCP) para verificar status do pregão;
- Registrar datas de abertura, empresa vencedora e valor final;
- Atualizar status: *aberto*, *em julgamento*, *adjudicado*, *fracassado*, *deserto*;
- Gerar notificações de mudança de status;
- Garantir auditabilidade completa de cada consulta realizada.

A equipe é composta por estudantes de uma faculdade pública (FACAPE), com experiência limitada em sistemas distribuídos. O prazo é acadêmico e os recursos de infraestrutura são restritos.

---

## Decisão

Adotamos **monolito modular** como estilo arquitetural para o módulo G07. O sistema será organizado em módulos internos bem definidos (consulta, status, notificação, auditoria), mas implantado como uma única unidade.

---

## Opções Consideradas

### Opção A: Monolito Modular
- **Descrição:** Aplicação única com pacotes/módulos internos separados por responsabilidade. Deploy em um único servidor ou container.
- **Prós:** Simples de desenvolver e implantar; fácil de depurar; sem overhead de rede entre módulos; adequado para equipes pequenas.
- **Contras:** Escalabilidade limitada; módulos podem se acoplar indevidamente se não houver disciplina; deploy único pode derrubar tudo.

### Opção B: Microsserviços
- **Descrição:** Cada responsabilidade (consulta, status, notificação) vira um serviço independente com sua própria API e banco de dados.
- **Prós:** Alta escalabilidade; falhas isoladas; tecnologias heterogêneas por serviço.
- **Contras:** Complexidade operacional alta (service mesh, discovery, circuit breaker); exige equipe experiente em DevOps; overhead desproporcional para o escopo acadêmico.

### Opção C: Serverless (FaaS)
- **Descrição:** Funções independentes ativadas por eventos (novo status, novo pregão) em plataforma como AWS Lambda ou similar.
- **Prós:** Custo por uso; sem servidor para gerenciar; natural para eventos assíncronos.
- **Contras:** Vendor lock-in; cold starts impactam monitoramento contínuo; dificulta auditoria local; fora do alcance de infraestrutura institucional da FACAPE.

---

## Critérios de Decisão

| Critério | Peso | Monolito Modular | Microsserviços | Serverless |
|----------|------|-----------------|----------------|------------|
| Facilidade de desenvolvimento | Alto | ✅ | ❌ | ⚠️ |
| Auditabilidade e rastreabilidade | Alto | ✅ | ⚠️ | ⚠️ |
| Custo de implantação | Médio | ✅ | ❌ | ⚠️ |
| Adequação ao tamanho da equipe | Alto | ✅ | ❌ | ⚠️ |
| Manutenibilidade futura | Médio | ⚠️ | ✅ | ❌ |

---

## Consequências

### Positivas
- Desenvolvimento mais rápido para o prazo acadêmico;
- Auditoria centralizada e fácil de implementar;
- Menor curva de aprendizado para a equipe.

### Negativas / Riscos
- Se o escopo crescer muito, pode ser necessária uma migração futura;
- Disciplina de módulos precisa ser mantida ativamente pela equipe.

### Neutras / Trade-offs
- A modularidade interna facilita uma eventual migração para microsserviços se necessário.

---

## Conformidade com a Lei 14.133/2021

O monolito modular facilita a implementação de um módulo de auditoria centralizado, garantindo que todas as consultas ao pregão e alterações de status sejam registradas com data, hora e resultado — em conformidade com os princípios de transparência e rastreabilidade exigidos pela lei.

---

*Referências:*  
- [Lei nº 14.133/2021](https://www.planalto.gov.br/ccivil_03/_ato2019-2022/2021/lei/l14133.htm)  
- Martin Fowler — *Monolith First* — https://martinfowler.com/bliki/MonolithFirst.html
