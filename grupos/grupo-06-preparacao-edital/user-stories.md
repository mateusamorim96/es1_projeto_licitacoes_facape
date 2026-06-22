# User Stories

## EP-01 - Formatação de Edital

### US-01 - Gerar edital automaticamente

Como servidor responsável pela licitação,
quero gerar automaticamente o edital a partir do TR e do Mapa de Riscos,
para reduzir erros manuais na elaboração do documento.

**Regras de Negócio Associadas:**
- **RN-07**: O prazo mínimo de divulgação do edital antes do pregão é de 8 dias úteis.

Critérios de Aceite:

Cenário 1: Geração bem-sucedida
  Dado que o TR e o Mapa de Riscos foram recebidos e validados
  E a data planejada para o pregão respeita o prazo mínimo de 8 dias úteis (RN-07)
  Quando solicito a geração do edital
  Então o sistema cria o edital no formato oficial padrão
  E associa o TR e o Mapa de Riscos a esse novo edital
  E define o status do edital como "Rascunho"

Cenário 2: Data do pregão inválida
  Dado que o TR e o Mapa de Riscos foram recebidos
  Quando informo uma data para o pregão que seja menor que 8 dias úteis a partir de hoje (violando a RN-07)
  Então o sistema bloqueia a geração do edital
  E exibe um alerta informando que a data do pregão viola o prazo mínimo legal de 8 dias úteis

Estimativa: M
Prioridade: Alta

---

### US-02 - Visualizar edital formatado

Como servidor responsável pela licitação,
quero visualizar o edital formatado antes do envio,
para conferir se as informações estão corretas.

Critérios de Aceite:

Cenário 1: Visualização disponível
  Dado que o edital foi gerado e está com o status "Rascunho" ou "Apto para Envio"
  Quando acesso a tela de visualização
  Então o sistema exibe o documento completo formatado (ex.: preview em PDF) para conferência

Cenário 2: Edital não gerado
  Dado que o processo ainda não teve seu edital gerado
  Quando tento acessar a tela de visualização do edital
  Então o sistema exibe um aviso informando que o edital deve ser gerado antes da visualização
  E mantém a opção de visualização desabilitada

Estimativa: S
Prioridade: Alta

---

## EP-02 - Gestão de Anexos

### US-03 - Anexar e organizar documentos obrigatórios

Como servidor responsável pela licitação,
quero anexar o Termo de Referência (TR) e o Mapa de Riscos ao edital,
para assegurar que a documentação que acompanha o processo esteja completa e centralizada.

Critérios de Aceite:

Cenário 1: Upload de documento obrigatório com sucesso
  Dado que estou na tela de edição do edital
  Quando seleciono e envio um arquivo PDF com o TR ou com o Mapa de Riscos
  Então o sistema anexa o arquivo ao edital correspondente
  E atualiza a listagem de anexos obrigatórios mostrando o status do documento como "Anexado"

Cenário 2: Formato de arquivo não aceito
  Dado que estou na tela de edição do edital
  Quando tento fazer o upload de um arquivo com formato diferente de PDF
  Então o sistema bloqueia o upload
  E exibe a mensagem: "Formato inválido. Apenas arquivos no formato PDF são aceitos para anexação."

Estimativa: M
Prioridade: Alta

---

## EP-03 - Validação de Completude

### US-04 - Validar documentos obrigatórios e consistência do edital

Como servidor responsável pela licitação,
quero validar a presença dos documentos obrigatórios e o preenchimento de todos os dados do edital,
para evitar o envio de processos incompletos ou com erros à Prefeitura.

Critérios de Aceite:

Cenário 1: Todos os documentos e informações presentes
  Dado que o edital possui todos os anexos obrigatórios (TR e Mapa de Riscos anexados)
  E todos os campos obrigatórios preenchidos
  Quando realizo a validação do edital
  Então o sistema altera o status do edital para "Apto para Envio"
  E habilita a funcionalidade de encaminhamento externo

Cenário 2: Documento ausente ou campos inválidos
  Dado que o TR ou o Mapa de Riscos não foi anexado
  Quando realizo a validação do edital
  Então o sistema exibe a listagem de inconsistências (ex.: "Mapa de Riscos ausente")
  E bloqueia a alteração do status para "Apto para Envio"
  E mantém desabilitada a funcionalidade de envio à Prefeitura

Estimativa: M
Prioridade: Alta

---

## EP-04 - Envio para Prefeitura

### US-05 - Enviar edital para Prefeitura via 1doc

Como servidor responsável pela licitação,
quero enviar o edital validado para o sistema 1doc da Prefeitura,
para que a Prefeitura conduza a realização do pregão eletrônico.

Critérios de Aceite:

Cenário 1: Envio realizado com sucesso
  Dado que o edital foi validado e está com o status "Apto para Envio"
  Quando aciono a opção de envio à Prefeitura
  Então o sistema realiza a transmissão dos dados e dos arquivos anexados via API/Integração com 1doc
  E atualiza o status do edital para "Enviado à Prefeitura"
  E exibe o número de protocolo e comprovante de envio gerado pelo 1doc

Cenário 2: Falha na integração de rede/sistema externo
  Dado que o edital está com status "Apto para Envio"
  Quando aciono a opção de envio à Prefeitura mas a integração externa com o 1doc está indisponível
  Então o sistema exibe uma mensagem de erro: "Não foi possível conectar ao sistema da Prefeitura. Tente novamente mais tarde."
  E mantém o status do edital como "Apto para Envio"
  E registra a falha técnica no log do sistema

Estimativa: L
Prioridade: Alta

---

## EP-05 - Auditoria

### US-06 - Registrar histórico de envio e ações do edital

Como auditor do sistema,
quero consultar o histórico de envios e alterações dos editais,
para garantir a transparência e a rastreabilidade do processo licitatório.

Critérios de Aceite:

Cenário 1: Consulta de histórico com dados
  Dado que houveram alterações ou envios de editais pelo módulo
  Quando acesso o painel de histórico de auditoria
<<<<<<< HEAD
  Então o sistema exibe em ordem cronológica o usuário responsável, ação realizada (geração, validação, envio), data, horário, versão do edital e o código de protocolo/confirmação de recebimento
=======
  Então o sistema exibe em ordem cronológica o usuário responsável, ação realizada (geração, validação, envio), data, horário, versão do edital e o código de protocolo/confirmação de recebimento.
>>>>>>> e65149434e40ecbf5cf8ae6f2992a67b7132243d

Cenário 2: Sem registros no histórico
  Dado que o módulo foi iniciado recentemente e nenhuma ação de alteração ou envio ocorreu
  Quando acesso o painel de histórico de auditoria
  Então o sistema exibe a mensagem "Nenhuma ação registrada para este processo de edital"

Estimativa: S
Prioridade: Média