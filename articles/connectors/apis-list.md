<properties
    pageTitle="Lista de conectores geridos pela Microsoft para utilização em Logic Apps do Microsoft Azure | App Service do Microsoft Azure"
    description="Obtenha uma lista completa dos conectores geridos pela Microsoft que pode utilizar para criar Logic Apps no App Service do Azure"
    services="app-service\logic"
    documentationCenter=""
    authors="MSFTMAN"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
    ms.service="app-service-logic"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/31/2016"
    ms.author="deonhe"/>

# Lista de conectores geridos

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema de pré-visualização 2015-08-01 das Logic Apps. Para a versão do esquema de pré-visualização 2014-12-01, clique em [Lista de Conectores](../app-service-logic/app-service-logic-connectors-list.md). 

Para obter informações sobre preços e uma lista do que está incluído com cada Escalão de Serviços, consulte [Preços dos Serviços de Aplicações do Azure](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Se pretende começar com Azure Logic Apps antes de se inscrever para uma conta do Azure, aceda a [Experimentar Aplicação Lógica](https://tryappservice.azure.com/?appservice=logic). Pode criar de imediato uma aplicação lógica de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.

Selecione um ícone para saber como pode rapidamente tirar partido destes conectores para compilar aplicações que chamam estes serviços. Estes conectores podem ser utilizados para compilar Logic Apps, PowerApps e Fluxos.

|Conectores||||
|-----------|-----------|-----------|-----------|
|[![Ícone da API][blobicon]<br/>**Blob do Azure**][azureblobdoc]|[![Ícone da API][boxicon]<br/>**Box**][boxDoc]|[![Ícone da API][crmonlineicon]<br/>**CRM Online**][crmonlinedoc]|[![Ícone da API][dropboxicon]<br/>**Dropbox**][dropboxdoc]|
|[![Ícone da API][facebookicon]<br/>**Facebook**][facebookdoc]|[![Ícone da API][ftpicon]<br/>**FTP**][ftpdoc]|[![Ícone da API][githubicon]<br/>**GitHub**][githubdoc]|[![Ícone da API][googledriveicon]<br/>**Google Drive**][googledrivedoc]|
|[![Ícone da API][mailchimpicon]<br/>**MailChimp**][mailchimpdoc]|[![Ícone da API][microsofttranslatoricon]<br/>**Tradutor**][microsofttranslatordoc]|[![Ícone da API][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|[![Ícone da API][office365icon]<br/>**Office 365**<br/>**Utilizadores**][office365usersdoc]|
|[![Ícone da API][office365icon]<br/>**Office 365**<br/>**Vídeo**][office365videodoc]|[![Ícone da API][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![Ícone da API][onedriveicon]<br/>**OneDrive<br/>para Negócios**][onedriveforbusinessdoc]|[![Ícone da API][outlookicon]<br/>**Outlook**][outlookdoc]|
|[![Ícone da API][projectonlineicon]<br/>**Project<br/>Online**][projectonlinedoc]|[![Ícone da API][rssicon]<br/>**RSS**][rssdoc]|[![Ícone da API][salesforceicon]<br/>**Salesforce**][salesforcedoc]|[![Ícone da API][sendgridicon]<br/>**SendGrid**][sendgriddoc]|
|[![Ícone da API][servicebusicon]<br/>**Service Bus**][servicebusdoc]|[![Ícone da API][sftpicon]<br/>**SFTP**][sftpdoc]|[![Ícone da API][sharepointicon]<br/>**SharePoint**<br/>**Online**][sharepointdoc]|[![Ícone da API][slackicon]<br/>**Slack**<br/>][slackdoc]|
|[![Ícone da API][smtpicon]<br/>**SMTP**][smtpdoc]|[![Ícone da API][sqlicon]<br/>**SQL Azure**][sqldoc]|[![Ícone da API][trelloicon]<br/>**Trello**][trellodoc]|[![Ícone da API][twilioicon]<br/>**Twilio**][twiliodoc]|
|[![Ícone da API][twittericon]<br/>**Twitter**][twitterdoc]|[![Ícone da API][wunderlisticon]<br/>**Wunderlist**][wunderlistdoc]|[![Ícone da API][yammericon]<br/>**Yammer**][yammerdoc] | |

> [AZURE.NOTE] Se tem utilizado o esquema de pré-visualização 2014-12-01 para criar Logic Apps, irá notar que os conectores de integração empresarial, tais como do BizTalk, não estão listados acima. Sabemos que estes são importantes e estamos a esforçar-nos para corrigir rapidamente esta situação. Apesar de não termos uma data de disponibilidade exata, queremos que saiba que esta é uma das nossas principais prioridades. Entretanto, pode aceder às suas [APIs v1 e BizTalk a partir das Logic Apps](https://blogs.msdn.microsoft.com/logicapps/2016/02/25/accessing-v1-apis-and-biztalk-apis-from-logic-apps/). Obrigado pela sua compreensão. Esteja atento.


### Os conectores podem ser acionadores
Vários conectores fornecem acionadores que podem notificar a sua aplicação quando ocorrem eventos específicos. Por exemplo, o conector FTP tem o acionador OnUpdatedFile. Pode compilar uma aplicação lógica, PowerApp ou Fluxo que fica à escuta deste acionador e efetua uma ação sempre que este é acionado.

Existem dois tipos de acionadores:  

* Acionadores de Consultas: estes acionadores consultam o serviço a uma frequência especificada para verificar se existem novos dados. Quando estão disponíveis novos dados, é executada uma nova instância da aplicação com a entrada desses dados. Para impedir que os mesmos dados sejam consumidos várias vezes, o acionador pode limpar os dados que foram lidos e transmitidos para a aplicação.
* Acionadores Push: estes acionadores estão à escuta de dados num ponto final ou da ocorrência de um evento. Em seguida, acionam uma nova instância da aplicação. O conector do Twitter é um exemplo desse tipo.


### Os conectores podem ser ações
Os conectores também podem ser utilizados como ações nas aplicações. As ações são úteis para procurar dados que podem ser utilizados na execução da aplicação. Por exemplo, pode precisar de procurar dados de clientes numa SQL Database durante o processamento de uma ordem. Alternativamente, pode ter de escrever, atualizar ou eliminar dados numa tabela de destino. Pode fazê-lo utilizando as ações fornecidas pelos conectores. Mapa de ações para operações que são definidas nos metadados Swagger.


[Novidades](../app-service-logic/app-service-logic-schema-2015-08-01.md)  
[Compilar agora uma aplicação lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)  
[Introdução imediata às PowerApps](../power-apps/powerapps-get-started-azure-portal.md)  
[Migrar Logic Apps existentes para a versão mais recente do esquema](connectors-schema-migration.md) 

<!--Connectors Documentation-->
[azureblobdoc]: ./connectors-create-api-azureblobstorage.md "Ligue-se ao blob do Azure para gerir ficheiros no contentor de blob."
[bingsearchDoc]: ./connectors-create-api-bingsearch.md "Procure no Bing imagens, notícias e vídeos."
[boxDoc]: ./connectors-create-api-box.md "Liga-se ao Box e pode carregar, obter, eliminar, listar tarefas de ficheiros e muito mais."
[crmonlinedoc]: ./connectors-create-api-crmonline.md "Ligue-se ao Dynamics CRM Online e faça mais com os seus dados CRM Online."
[dropboxdoc]: ./connectors-create-api-dropbox.md "Ligue-se ao Dropbox para obter, eliminar, listar tarefas de ficheiros e muito mais."
[exceldoc]: ./connectors-create-api-excel.md "Ligue-se ao Excel."
[facebookdoc]: ./connectors-create-api-facebook.md "Ligue-se ao Facebook para publicar numa linha cronológica, obter o feed de uma página e muito mais."
[ftpdoc]: ./connectors-create-api-ftp.md "Liga-se a um servidor FTP/FTPS e realize diferentes tarefas FTP, incluindo carregar, obter, eliminar ficheiros e muito mais."
[googledrivedoc]: ./connectors-create-api-googledrive.md "Ligue-se ao Google Drive e interaja com os seus dados."
[microsofttranslatordoc]: ./connectors-create-api-microsofttranslator.md
[office365outlookdoc]: ./connectors-create-api-office365-outlook.md "O Conector do Office 365 pode enviar e receber e-mails, gerir o calendário e os contactos com a conta do Office 365."
[officeunifieddoc]: ./connectors-create-api-bingsearch.md
[office365usersdoc]: ./connectors-create-api-office365-users.md
[office365videodoc]: ./connectors-create-api-office365-video.md
[onedrivedoc]: ./connectors-create-api-onedrive.md "Liga-se ao seu Microsoft OneDrive e carrega, elimina, lista ficheiros e muito mais."
[onedriveforbusinessdoc]: ./connectors-create-api-onedriveforbusiness.md "Liga-se ao seu Microsoft OneDrive empresarial e carrega, elimina, lista ficheiros e muito mais."
[outlookdoc]: ./connectors-create-api-outlook.md "Ligue-se à sua caixa de correio do Outlook e aceda ao e-mail, entre outros."
[projectonlinedoc]: ./connectors-create-api-projectonline.md "Liga-se ao Microsoft Project Online."
[rssdoc]: ./connectors-create-api-rss.md "O conector RSS permite que os utilizadores publiquem e obtenham itens do feed. Também permite que os utilizadores acionem operações quando um novo item é publicado no feed."
[salesforcedoc]: ./connectors-create-api-salesforce.md "Ligue-se à conta Salesforce e gira contas, responsáveis, oportunidades e muito mais."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Liga-se ao Microsoft Project Online."
[servicebusdoc]: ./connectors-create-api-servicebus.md "Pode enviar mensagens de Filas e Tópicos do Service Bus e receber mensagens de Filas e Subscrições do Service Bus."
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Liga-se ao SharePoint Online para gerir documentos e listar itens."
[slackdoc]: ./connectors-create-api-slack.md "Ligue-se ao Slack e publique mensagens nos canais Slack."
[sftpdoc]: ./connectors-create-api-sftp.md "Ligue-se ao SFTP para carregar, obter, eliminar ficheiros e muito mais."
[githubdoc]: ./connectors-create-api-github.md "Liga-se ao GitHub para controlar problemas."
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "Enviar melhor e-mail."
[smtpdoc]: ./connectors-create-api-smtp.md "Liga-se a um servidor SMTP e pode enviar e-mails com anexos."
[sqldoc]: ./connectors-create-api-sqlazure.md "Liga-se à SQL Database do Azure. Pode criar, atualizar, obter e eliminar entradas numa tabela da SQL Database."
[trellodoc]: ./connectors-create-api-trello.md "O Trello é a forma gratuita, flexível e visual de organizar qualquer coisa com qualquer pessoa."
[twiliodoc]: ./connectors-create-api-twilio.md "Liga-se ao Twilio e pode enviar e receber mensagens, obter números disponíveis, gerir números de telefone recebidos, entre outros."
[twitterdoc]: ./connectors-create-api-twitter.md "Liga-se ao Twitter e obtém linhas cronológicas, publica tweets, entre outros."
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Mantém a sua vida sincronizada."
[yammerdoc]: ./connectors-create-api-yammer.md "Liga-se ao Yammer para publicar mensagens e receber novas mensagens."

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[githubicon]: ./media/apis-list/githubicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[mailchimpicon]: ./media/apis-list/mailchimpicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[onedriveforbusinessicon]: ./media/apis-list/onedriveforbusinessicon.png
[outlookicon]: ./media/apis-list/outlookicon.png
[projectonlineicon]: ./media/apis-list/projectonlineicon.png
[rssicon]: ./media/apis-list/rssicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[sendgridicon]: ./media/apis-list/sendgridicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[trelloicon]: ./media/apis-list/trelloicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[wunderlisticon]: ./media/apis-list/wunderlisticon.png
[yammericon]: ./media/apis-list/yammericon.png



<!--HONumber=Jun16_HO2-->


