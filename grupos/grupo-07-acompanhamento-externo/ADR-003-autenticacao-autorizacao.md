# ADR-003 — Autenticação e Autorização

**Data:** 2026-04-22  
**Status:** Aceita  
**Autores:** Grupo 07

---

## Contexto

O módulo G07 precisa controlar quem pode visualizar e atualizar os dados do acompanhamento do pregão. No contexto da FACAPE, há múltiplos perfis de usuários que interagem com o processo licitatório:

- **Setor de Licitações:** acompanha e atualiza o status do pregão;
- **Fiscais e Gestores de Contrato:** recebem notificações e consultam resultado;
- **Demandantes (solicitantes):** visualizam o andamento do que solicitaram;
- **Contabilidade:** consulta valores finais para fins orçamentários.

Cada perfil deve ter acesso apenas às funcionalidades pertinentes. O sistema tramita documentos pelo 1doc e se comunica com outros módulos (G06 e G08).

---

## Decisão

Adotamos **JWT (JSON Web Tokens) com controle de papéis (RBAC)** como estratégia de autenticação e autorização. Os tokens serão emitidos pelo próprio sistema após login com credenciais, e cada token carregará os papéis (roles) do usuário para controle de acesso nos endpoints.

---

## Opções Consideradas

### Opção A: JWT próprio com RBAC
- **Descrição:** O sistema emite tokens JWT após autenticação local. Os papéis (licitações, fiscal, demandante, contabilidade) são embutidos no token e verificados a cada requisição.
- **Prós:** Simples de implementar; stateless; sem dependência de infraestrutura externa; amplamente documentado.
- **Contras:** Gestão de segredos (secret key) precisa de cuidado; revogação de token exige lista negra ou tokens de curta duração.

### Opção B: SSO Institucional (LDAP/AD da Prefeitura)
- **Descrição:** Integração com o Active Directory ou LDAP da Prefeitura para autenticação centralizada.
- **Prós:** Usuários usam as mesmas credenciais institucionais; gestão centralizada de usuários.
- **Contras:** Dependência de infraestrutura da Prefeitura que pode não estar acessível para o projeto acadêmico; complexidade de configuração (LDAP bind, SSL); escopo além do módulo G07.

### Opção C: OAuth2 com provedor externo
- **Descrição:** Delegação de autenticação para um provedor OAuth2 (Google, Microsoft, Gov.br).
- **Prós:** Elimina gestão de senhas; padrão moderno e seguro.
- **Contras:** Dependência de serviço externo; usuários institucionais podem não ter conta no provedor escolhido; complexidade adicional de configuração de cliente OAuth.

---

## Critérios de Decisão

| Critério | Peso | JWT + RBAC | SSO/LDAP | OAuth2 |
|----------|------|-----------|----------|--------|
| Facilidade de implementação | Alto | ✅ | ❌ | ⚠️ |
| Independência de infraestrutura externa | Alto | ✅ | ❌ | ⚠️ |
| Granularidade de permissões | Alto | ✅ | ⚠️ | ⚠️ |
| Segurança | Médio | ⚠️ | ✅ | ✅ |
| Adequação ao contexto acadêmico | Alto | ✅ | ❌ | ⚠️ |

---

## Consequências

### Positivas
- Controle de acesso por perfil sem dependência de sistemas externos;
- Fácil de testar e documentar;
- Tokens carregam informações suficientes para logs de auditoria (quem fez o quê).

### Negativas / Riscos
- Necessidade de definir e manter os papéis corretamente na aplicação;
- Tokens de longa duração representam risco se vazados — mitigado com expiração curta (ex.: 1h) e refresh tokens.

### Neutras / Trade-offs
- A solução pode ser evoluída para integração com SSO institucional no futuro sem grandes mudanças na lógica de negócio, apenas substituindo o provedor de tokens.

---

## Conformidade com a Lei 14.133/2021

O controle de acesso por papéis garante que apenas usuários autorizados possam registrar ou alterar dados do pregão, atendendo ao princípio de responsabilidade e rastreabilidade dos atos administrativos exigidos pela lei.

---

*Referências:*  
- [RFC 7519 — JSON Web Token (JWT)](https://datatracker.ietf.org/doc/html/rfc7519)  
- OWASP — Authentication Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html
