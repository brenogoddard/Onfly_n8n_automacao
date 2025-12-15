Teste Técnico – Automação n8n | Onfly

Autor: Breno Goddard


Tarefa – Automatização de E-mails Cenário: O time Financeiro da Onfly recebe diariamente um e-mail com um anexo em CSV contendo dados financeiros.


Visão Geral do Fluxo:

1. O workflow é acionado quando um novo e-mail chega via IMAP.
2. O fluxo verifica se o e-mail possui anexo CSV.
3. Caso exista:
   - O arquivo é salvo no Google Drive.
   - A cotação do dólar do dia é consultada via API pública.
   - Um e-mail de confirmação é enviado ao remetente com a cotação.
4. Caso não exista anexo:
   - Um e-mail de aviso é enviado informando a ausência do arquivo.

---------------------------------------------------------------------------------------------

Estrutura do Workflow:

Email Trigger (IMAP) 
-> SET (garantir o nome do arquivo) 
-> IF (verifica existência de anexo)  

-> TRUE
- Upload do arquivo no Google Drive
- HTTP Request (API de cotação USD)
- Send Email (confirmação com cotação)

-> FALSE
- Send Email (aviso de ausência de anexo)

---------------------------------------------------------------------------------------------

Tratamento dos Anexos (Node Set)

Para garantir a integridade das informações do anexo ao longo do workflow, utilizei um node Set logo após o gatilho do e-mail.

Objetivo do Set:

- Preservar o nome do arquivo CSV em formato JSON.

- Permitir o uso do nome do arquivo em etapas posteriores, como no envio do e-mail de confirmação.

Funcionamento:

- O e-mail recebido via IMAP contém o anexo no contexto binário.

- O node Set extrai dinamicamente o nome do arquivo e o salva no campo JSON fileName.

- O contexto binário original é mantido para permitir o upload do arquivo no Google Drive.

- O campo fileName passa a ser utilizado como referência confiável nos nodes seguintes.

- Verifica se existe anexo no e-mail.

- Puxa o nome do primeiro arquivo recebido.

- Evita falhas em casos onde o e-mail não possui anexo.

- O parâmetro Include Other Input Fields foi mantido ativado para garantir que o contexto binário continuasse disponível para o node de upload no Google Drive.

---------------------------------------------------------------------------------------------

API Utilizada:

API pública de câmbio:
https://api.exchangerate-api.com/v4/latest/USD

Valor utilizado no fluxo:
rates.BRL
rates.USD

---------------------------------------------------------------------------------------------

Configurações Necessárias:

E-mail:
- Credenciais IMAP para leitura da caixa de entrada.
- Credenciais SMTP para envio das respostas.
- Remetente configurável no node Send Email.

Google Drive:
- Credencial OAuth configurada no n8n.
- Pasta de destino configurável no node de upload.

---------------------------------------------------------------------------------------------

Credenciais de E-mail:

- As credenciais de leitura (IMAP) e envio (SMTP) foram configuradas diretamente nos nodes Email Trigger (IMAP) e Send Email, utilizando o gerenciador de credenciais do n8n.

- O workflow é independente do provedor de e-mail, podendo ser utilizado com Gmail, Outlook ou outros compatíveis.

Remetente de Saída:

- O endereço de e-mail remetente foi configurado no campo From Email do node Send Email.

- Isso permite alterar o remetente sem impactar o restante do fluxo.

- Utilizar diferentes contas de envio (por exemplo, e-mails corporativos ou contas de teste) apenas ajustando as credenciais SMTP associadas;

- Manter o fluxo desacoplado do provedor de e-mail, facilitando a reutilização do workflow em diferentes ambientes (desenvolvimento, homologação ou produção).

Pasta de Destino (Google Drive):

- A pasta de destino do arquivo foi configurada no node Google Drive – Upload File.

- O fluxo pode ser facilmente adaptado para armazenamento local, substituindo o node de upload por um node em diretório local, sem alterações na lógica principal.

---------------------------------------------------------------------------------------------

Tratamento de Exceções:

- Caso o e-mail não possua anexo CSV, o workflow envia uma resposta informando a ausência do arquivo.
- Cada e-mail gera apenas uma resposta, evitando notificações duplicadas.

---------------------------------------------------------------------------------------------

Observação: 

Durante os testes em ambiente local, a API https://open.er-api.com/v6/latest/USD apresentou instabilidade de conexão (timeout relacionado à resolução IPv6). Para garantir a execução completa do fluxo conforme o objetivo do teste, utilizei uma API pública alternativa de câmbio https://api.exchangerate-api.com, amplamente documentada e com o mesmo propósito, mantendo o uso exclusivo de dados públicos e a lógica solicitada.


---------------------------------------------------------------------------------------------

Considerações Finais:

A automação foi desenvolvida utilizando exclusivamente nodes nativos do n8n, com foco em clareza e boas práticas.