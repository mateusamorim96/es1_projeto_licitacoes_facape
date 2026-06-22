# Backlog

### US01: Integração Automática com ETP e Ata SRP
* **Como** Analista de Licitação da FACAPE,
  **Quero** que o sistema extraia e carregue automaticamente os dados consolidados do Estudo Técnico Preliminar (Módulo G02) e as decisões de Ata SRP (Módulo G03),
  **Para** iniciar a montagem do Termo de Referência sem a necessidade de redigitar informações manuais, evitando erros de transcrição.
* **Critério de Aceite:**
  - Dado que o ETP-2026-0112 foi finalizado pelo G02 e a Ata SRP correspondente está disponível no G03;
  - Quando o analista disparar o gatilho "Gerar novo TR";
  - Então o sistema deve popular a tela do TR com a descrição dos itens, quantitativos e estimativas de preço de forma fidedigna.

---

### US02: Divisão Automatizada de Cotas por Faixa de Valor
* **Como** Motor de Regras do Sistema,
  **Quero** consultar as variáveis financeiras da tabela de parâmetros legislativos e aplicar a reserva de mercado (100% exclusiva para itens até R$ 80.000,00 ou divisão 25%/75% para valores superiores),
  **Para** garantir compliance instantâneo com as regras de ME/EPP da Lei Complementar nº 123/2006 (Art. 48) por força da Lei nº 14.133/2021 (Art. 4º).
* **Critério de Aceite:**
  - Dado um item cujo valor total estimado é de R$ 90.000,00 (acima do teto);
  - Quando a rotina de processamento for acionada;
  - Então o sistema deve aplicar o percentual de 25% para ME/EPP e 75% para Ampla Concorrência, registrando o ID da lei associada no banco.

---

### US03: Algoritmo de Arredondamento com Margem de Proteção (Ceil)
* **Como** Desenvolvedor do Módulo de Domínio do TR,
  **Quero** implementar a função de arredondamento matemático para cima (`Math.ceil`) sobre frações geradas na divisão de itens indivisíveis em favor da `CotaExclusiva`,
  **Para** blindar o edital contra impugnações e assegurar o piso legal estabelecido de proteção aos pequenos negócios.
* **Critério de Aceite:**
  - Dado a divisão de um lote indivisível com quantidade total de 10 unidades (25% = 2.5);
  - Quando o motor calcular as frações;
  - Então o resultado final gravado deve alocar exatamente 3 itens para a cota de pequenas empresas e o saldo (7 itens) para ampla concorrência (Conforme **ADR-01**).

---

### US04: Versionamento de Regras e Imutabilidade Legal
* **Como** Auditor de Controle Interno,
  **Quero** que o Termo de Referência salve internamente e de forma imutável a versão da legislação utilizada em seus cálculos,
  **Para** manter a memória de cálculo histórica intacta se houver mudanças futuras nos valores limites da lei por decreto (Conforme **ADR-02**).
* **Critério de Aceite:**
  - Dado um TR gerado sob as regras da tabela parametrizada `V_LEI_14133_REG_MEEPP_2026_01`;
  - Quando o administrador alterar os limites globais do sistema no futuro para R$ 100.000,00;
  - Então a consulta retroativa a este TR antigo deve exibir os cálculos e o histórico baseados no limite original de R$ 80.000,00.

---

### US05: Disparo de Eventos para Trilha de Auditoria (Contrato G09)
* **Como** Barramento de Comunicação do Sistema,
  **Quero** enviar um payload JSON estruturado contendo a assinatura do usuário, ações e snapshots de dados a cada alteração de estado do TR,
  **Para** munir o módulo central do **Grupo 09 (Auditoria, Compliance e Indicadores)** com logs auditáveis em tempo real.
* **Critério de Aceite:**
  - Dado que o analista alterou o estado de um TR de *Rascunho* para *Consolidado Técnico*;
  - Quando o método de persistência concluir a transação no banco;
  - Então um log estruturado em formato Chave-Valor (`[INFO] [COMPLIANCE_G04]`) e um payload devem ser transmitidos à API de monitoramento com sucesso (RQ-INT-05).