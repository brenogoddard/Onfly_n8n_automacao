Teste Técnico – Automação n8n | Onfly

Autor: Breno Goddard

Objetivo:
Desenvolver uma automação no n8n para processar e-mails com anexo CSV, salvar o arquivo no Google Drive e responder ao remetente com a cotação do dólar do dia (USD → BRL).

Visão Geral do Fluxo:

1. O workflow é acionado quando um novo e-mail chega via IMAP.
2. O fluxo verifica se o e-mail possui anexo CSV.
3. Caso exista:
   - O arquivo é salvo no Google Drive.
   - A cotação do dólar do dia é consultada via API pública.
   - Um e-mail de confirmação é enviado ao remetente com a cotação.
4. Caso não exista anexo:
   - Um e-mail de aviso é enviado informando a ausência do arquivo.

----------------------------------------------------------------------------------------

Estrutura do Workflow:

Email Trigger (IMAP)  
-> IF (verifica existência de anexo)  

-> TRUE
- Upload do arquivo no Google Drive
- HTTP Request (API de cotação USD)
- Send Email (confirmação com cotação)

-> FALSE
- Send Email (aviso de ausência de anexo)

---------------------------------------------------------------------------------------------

API Utilizada:

API pública de câmbio:
https://api.exchangerate-api.com/v4/latest/USD

Valor utilizado no fluxo:
rates.BRL

---------------------------------------------------------------------------------------------

Configurações Necessárias:

E-mail:
- Credenciais IMAP para leitura da caixa de entrada
- Credenciais SMTP para envio das respostas
- Remetente configurável no node Send Email

Google Drive:
- Credencial OAuth configurada no n8n
- Pasta de destino configurável no node de upload

---------------------------------------------------------------------------------------------

Tratamento de Exceções:

- Caso o e-mail não possua anexo CSV, o workflow envia uma resposta informando a ausência do arquivo.
- Cada e-mail gera apenas uma resposta, evitando notificações duplicadas.