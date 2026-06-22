# ADR-004 — Armazenamento de Documentos

**Data:** 2026-04-22  
**Status:** Aceita  
**Autores:** Grupo 07

---

## Contexto

O módulo G07 recebe o edital enviado pelo G06 e registra o resultado do pregão para envio ao G08. No curso do processo licitatório descrito na aula, são gerados documentos como:

- Edital (PDF) recebido do G06;
- Ata de Registro de Preços ou Contrato (PDF) que fundamenta o resultado;
- Comprovantes de publicação no Portal de Compras Públicas;
- Registros de auditoria de consultas realizadas.

A Lei nº 14.133/2021 exige que todos os atos do processo licitatório sejam documentados com integridade e rastreabilidade. Na FACAPE, os documentos tramitam pelo sistema 1doc.

---

## Decisão

Adotamos **banco de dados relacional com armazenamento de referência de arquivo + sistema de arquivos local organizado** para os documentos. Metadados dos documentos (nome, tipo, data, hash SHA-256, usuário que enviou) ficam no banco de dados. Os arquivos binários (PDFs) ficam em diretório estruturado no servidor, com hash para verificação de integridade.

---

## Opções Consideradas

### Opção A: Banco de Dados com Referência + Sistema de Arquivos Local
- **Descrição:** Metadados no banco relacional; PDFs salvos em diretório estruturado (`/documentos/{ano}/{processo}/`). Hash SHA-256 do arquivo é armazenado no banco para verificação de integridade.
- **Prós:** Simples; sem custo de serviço externo; fácil backup; hash garante imutabilidade.
- **Contras:** Escalabilidade limitada (disco local); precisa de política de backup ativa.

### Opção B: BLOB no Banco de Dados
- **Descrição:** Os PDFs são armazenados diretamente como campos BYTEA/BLOB no banco relacional.
- **Prós:** Tudo em um lugar; transações ACID garantem consistência entre metadado e arquivo.
- **Contras:** Degrada performance do banco conforme cresce; backups ficam pesados; não é recomendado para arquivos grandes.

### Opção C: Armazenamento S3-compatível (MinIO ou similar)
- **Descrição:** Arquivos enviados para um serviço de object storage compatível com S3 (MinIO self-hosted ou AWS S3).
- **Prós:** Escalável; versionamento nativo; URLs de acesso controlado.
- **Contras:** Infraestrutura adicional (MinIO server ou conta AWS); overhead para o escopo acadêmico.

### Opção D: GED Externo (integração com 1doc)
- **Descrição:** Delegação do armazenamento ao sistema 1doc já utilizado pela FACAPE.
- **Prós:** Reaproveitamento de sistema existente; documentos já no fluxo institucional.
- **Contras:** Dependência de API do 1doc que pode não estar disponível para integração direta; escopo fora do controle do grupo.

---

## Critérios de Decisão

| Critério | Peso | Arquivo Local | BLOB no BD | S3/MinIO | GED/1doc |
|----------|------|--------------|-----------|----------|----------|
| Simplicidade de implementação | Alto | ✅ | ⚠️ | ⚠️ | ❌ |
| Integridade dos documentos | Alto | ✅ (hash) | ✅ | ✅ | ⚠️ |
| Custo de infraestrutura | Médio | ✅ | ✅ | ⚠️ | ✅ |
| Performance do banco | Alto | ✅ | ❌ | ✅ | ✅ |
| Independência de sistemas externos | Alto | ✅ | ✅ | ⚠️ | ❌ |

---

## Consequências

### Positivas
- Solução viável dentro da infraestrutura acadêmica disponível;
- Hash SHA-256 garante rastreabilidade e imutabilidade dos documentos armazenados;
- Metadados no banco permitem consultas e auditoria eficientes.

### Negativas / Riscos
- Disco local requer política de backup — responsabilidade da equipe de infraestrutura;
- Sem versionamento automático (pode ser implementado manualmente com cópias datadas).

### Neutras / Trade-offs
- A estrutura de metadados no banco é agnóstica ao storage backend, facilitando migração futura para S3 sem alterar a camada de negócio.

---

## Conformidade com a Lei 14.133/2021

O armazenamento com hash de integridade garante que documentos oficiais (editais, atas, contratos) não sejam alterados após o registro, atendendo ao Art. 7º da lei que prevê a publicidade e integridade dos atos administrativos. O registro do usuário responsável pelo upload complementa a rastreabilidade exigida.

---

*Referências:*  
- [Lei nº 14.133/2021, Art. 7º](https://www.planalto.gov.br/ccivil_03/_ato2019-2022/2021/lei/l14133.htm)  
- PostgreSQL — Large Objects vs BYTEA: https://www.postgresql.org/docs/current/largeobjects.html
