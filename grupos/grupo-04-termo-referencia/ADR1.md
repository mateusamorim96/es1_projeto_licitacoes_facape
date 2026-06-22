# Número do ADR: 001
## Título: Motor de Cálculo de Cotas e Arredondamento (Lei Complementar nº 123/2006)
*Data: 2026-06-11* *Responsável: Grupo 04 — Módulo Termo de Referência* Status: Aceito

### Contexto
Ao realizar a divisão proporcional de itens com valor total estimado acima de R$ 80.000,00, o sistema deve destinar obrigatoriamente 25% para a classe `CotaExclusiva` (destinada a ME/EPP) e 75% para a classe `CotaAberta` (Ampla Concorrência). O problema técnico e jurídico surge na divisão de frações de itens físicos indivisíveis na hora de gerar o Termo de Referência (por exemplo, aplicar 25% sobre a compra de 10 computadores ou veículos resulta em 2,5 itens). O sistema precisa decidir como tratar essa fração sem gerar inconsistências ou margem para erros humanos.

### Decisão
Ficou estabelecido arquiteturalmente no sistema que a engine (motor) de cálculo de cotas sempre aplicará a função matemática de arredondamento para cima (`Ceil`) em favor da classe `CotaExclusiva`. O sistema calculará o valor inteiro arredondado para a cota de pequenos negócios e, em seguida, subtrairá essa porção da quantidade total do lote para definir o saldo exato que restará para a ampla concorrência (`CotaAberta`).

*Exemplo prático:* 25% de 10 itens = 2,5 -> Aplica-se o `Ceil`, arredondando para 3 itens na cota exclusiva. Sobram exatamente 7 itens (10 - 3) alocados para a cota aberta.

### Justificativa
A escolha pela função `Ceil` em favor da cota exclusiva baseia-se nas seguintes razões:

- **Cumprimento do Piso Legal:** A legislação estabelece a reserva de *até* 25%. Arredondar para baixo faria com que o percentual real ficasse abaixo de 25% (ex: 2 de 10 seria 20%), violando o espírito de proteção e fomento aos pequenos negócios.
- **Automação Sem Erros:** Centralizar essa lógica em um componente de domínio (`MotorCalculoCotas`) impede que analistas de licitação façam arredondamentos arbitrários ou incorretos manualmente na montagem do TR.
- **Segurança Jurídica:** Reduz o risco de o Termo de Referência ser travado em fases posteriores (como na análise de risco do G05 ou parecer jurídico do Edital no G06), blindando o edital contra contestações.

### Alternativas Consideradas
Foram consideradas as seguintes alternativas:

- **Arredondamento Padrão (Round half up):** Se a fração for < 0.5 arredonda para baixo, se >= 0.5 para cima. Foi rejeitada porque, em um cenário de 9 itens (25% = 2,25), o sistema arredondaria para 2 itens (22,2%), ficando abaixo do piso legal exigido.
- **Arredondamento para Baixo (Floor):** Sempre favorecer a Ampla Concorrência. Totalmente rejeitada por violar diretamente as diretrizes de fomento às ME/EPP instituídas pela Lei Complementar nº 123/2006.

### Consequências
A escolha do algoritmo traz as seguintes consequências:

- **Benefícios de Conformidade:** Garante em código que o benefício concedido às ME/EPP nunca será inferior ao patamar mínimo, blindando o TR contra impugnações de grandes empresas concorrentes.
- **Tratamento de Exceções:** Lotes com quantidade total igual a 1 e valor superior a R$ 80.000,00 serão destinados 100% para cota exclusiva por força do `Ceil` (25% de 1 = 0,25 -> 1), o que é correto dada a indivisibilidade do objeto.

### Referências
- **Lei Complementar nº 123/2006 (Art. 48, III):** Estabelece a obrigatoriedade de reserva de cota de até 25% para microempresas e empresas de pequeno porte em certames de bens de natureza divisível.
- **Diretrizes do Projeto FACAPE (RQ-INT-03 - Compliance):** Exigência de acoplamento de fundamentos jurídicos nas decisões de arquitetura.