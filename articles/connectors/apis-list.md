---
title: Conectores para o Azure Logic Apps | Microsoft Docs
description: "Escolha de entre todos os conectores geridos pela Microsoft para compilar e criar aplicações lógicas"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f1f1fd50-b7f9-4d13-824a-39678619aa7a
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 1e2dad92647630c8b9a4e7676eee9288c87daf44
ms.contentlocale: pt-pt
ms.lasthandoff: 06/28/2017


---
<a id="connectors-list" class="xliff"></a>

# Lista de conectores
> [!TIP]
> A [lista completa de A a Z](#az) (neste tópico) inclui todos os conectores disponíveis que pode utilizar nas suas aplicações lógicas. Os [detalhes do conector](/connectors/) listam os acionadores e as ações definidas no swagger, bem como os limites para cada conector.

Os conectores fazem parte integral da criação de aplicações lógicas. Ao utilizá-los, pode realmente expandir as suas aplicações no local e na cloud, para que façam coisas diferentes com os dados que criar e com aqueles que já tem. Os conectores estão disponíveis nas seguintes categorias: 

* **Conectores padrão**: disponíveis e incluídos automaticamente quando utiliza aplicações lógicas. Alguns exemplos incluem Service Bus, Power BI, Oracle Database, OneDrive e muitos outros.

* **Conectores de contas de integração**: disponíveis quando comprar uma conta de integração. Com estes conectores, pode transformar e validar XML, processar mensagens B2B com AS2 / X12 / EDIFACT e codificar e descodificar ficheiros simples. Se trabalhar com o BizTalk Server, estes conectores são adequados para expandir os seus fluxos de trabalho do BizTalk para o Azure.  

    O BizTalk Server também tem um [adaptador do Logic Apps](https://msdn.microsoft.com/library/mt787163.aspx), que inclui a receção a partir de aplicações lógicas e o envio para aplicações lógicas.

* **Conectores empresariais**: inclui MQ e SAP. Disponível mediante custo adicional. 

Os artigos [Preços de Aplicações Lógicas](https://azure.microsoft.com/pricing/details/logic-apps/) e [Pricing model](../logic-apps/logic-apps-pricing.md) (Modelo de preços) disponibilizam mais detalhes sobre os custos. 

<a id="popular-connectors" class="xliff"></a>

## Conectores populares
Existem milhares de aplicações e milhões de execuções que utilizam estes conectores para processar dados e informações com êxito. A tabela seguinte lista os mais populares e alguns dos preferidos dos nossos utilizadores:

| |  |  |  |
| --- | --- | --- | --- |
| [![API Icon][AzureBlobStorageicon]<br/>**Armazenamento de Blobs do<br/>Azure**][AzureBlobStoragedoc] | Se quiser automatizar tarefas com a sua conta de armazenamento, deve ver este conector. Suporta operações de CRUD (criar, ler, atualizar, eliminar). | [![API Icon][Azure-Functionsicon]<br/>**Funções do Azure**][azure-functionsdoc] | Crie funções que executam fragmentos personalizados de C# ou node.js e utilize-as nas suas aplicações lógicas.  |
| [![API Icon][Dynamics-365icon]<br/>**Dynamics 365<br/>CRM Online**][Dynamics-365doc] | Um dos conectores mais requisitados. Tem acionadores e ações que ajudam a automatizar fluxos de trabalhos com oportunidades potenciais, entre outros. | [![API Icon][Event-Hubs-icon]<br/>**Hubs de Eventos**][event-hubs-doc] | Consuma e publique eventos num hub do Hub de Eventos. Por exemplo, pode utilizar o Hub de Eventos para obter resultados da sua aplicação lógica e, em seguida, enviar a saída para um fornecedor de análises em tempo real. |
| [![API Icon][FTPicon]<br/>**FTP**][FTPdoc] | Se o seu servidor FTP estiver acessível a partir da Internet, pode automatizar fluxos de trabalho para trabalhar com ficheiros e pastas. <br/><br/>O SFTP também está disponível com o conector SFTP. | [![API Icon][HTTPicon]<br/>**HTTP**][httpdoc] | Utilize aplicações lógicas para comunicar com qualquer ponto final através de HTTP. |
| [![API Icon][Office-365-Outlookicon]<br/>**Office 365<br/>Outlook**][office365-outlookdoc] | Muitos acionadores e muitas mais ações para utilizar o e-mail e os eventos do Office 365 nos seus fluxos de trabalho. <br/><br/>Este conector inclui uma ação de *e-mail de aprovação* para aprovar pedidos de férias, relatórios de despesas e assim sucessivamente. <br/><br/>Os utilizadores do Office 365 também estão disponíveis com o conector Office 365 Utilizadores.| [![API Icon][HTTP-Requesticon]<br/>**Request / Response**][HTTP-Requestdoc] | Este conector fornece um URL HTTPS. Quando a aplicação lógica recebe um pedido para este URL, é iniciada. |
| [![API Icon][Salesforceicon]<br/>**Salesforce**][salesforcedoc] | Inicie sessão facilmente com a sua conta do Salesforce para obter acesso a objetos, como oportunidades potenciais, entre outros. |  [![API Icon][Service-Busicon]<br/>**Service Bus**][Service-Busdoc] | O conector mais popular do Logic Apps, inclui acionadores e ações para enviar mensagens assíncronas e publcar/subscrever com filas, subscrições e tópicos. |
|  [![API Icon][SharePointicon]<br/>**SharePoint<br/>Online**][SharePointdoc] | Se fizer alguma coisa com o SharePoint e puder beneficiar de automatização, recomendamos ver este conector. Pode ser utilizado com o SharePoint no local e o SharePoint Online. | [![API Icon][SQL-Servericon]<br/>**SQL Server**][SQL-Serverdoc] | Um dos conectores mais utilizadores, pode ligar-se a ao SQL Server no local e a Bases de Dados SQL do Azure. | 
| [![API Icon][Twittericon]<br/>**Twitter**][Twitterdoc] | Inicie sessão facilmente com uma conta do Twitter e inicie um fluxo de trabalho quando for publicado um tweet novo. Depois, guarde esses tweets numa base de dados SQL ou numa lista do SharePoint. | | | 

<a id="integration-account-connectors" class="xliff"></a>

## Conectores de conta de integração 

O Enterprise Integration Pack (EIP) inclui conectores muito conhecidos junto da comunidade do BizTalk Server. Na compra de uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), também recebe os conectores seguintes: 

|  |  |  |  |
| --- | --- | --- | --- |
| [![API Icon][as2icon]<br/>**AS2</br> descodificar**][as2decode] | [![API Icon][as2icon]<br/>**AS2</br> codificar**][as2encode] | [![API Icon][x12icon]<br/>**EDIFACT</br> descodificar**][EDIFACTdecode] | [![API Icon][x12icon]<br/>**EDIFACT</br> codificar**][EDIFACTencode] |
[![API Icon][flatfileicon]<br/>**Codificação de ficheiro</br>simples**][flatfiledoc] | [![API Icon][flatfiledecodeicon]<br/>**Codificação de ficheiro</br>simples**][flatfiledecodedoc] | [![API Icon][integrationaccounticon]<br/>**Conta de<br/>integração**][integrationaccountdoc] | [![API Icon][xmltransformicon]<br/>**Transform<br/>XML**][xmltransformdoc] |
| [![API Icon][x12icon]<br/>**X12</br> descodificar**][x12decode] | [![API Icon][x12icon]<br/>**X12</br> codificar**][x12encode] | [![API Icon][xmlvalidateicon]<br/>**Validação de<br/>XML**][xmlvalidatedoc] | |

<a id="enterprise-connectors" class="xliff"></a>

## Conectores empresariais

Ligue às aplicações empresariais nas suas aplicações lógicas.

|  |  |
| --- | --- |
|[![API Icon][MQicon]<br/>**MQ**][mqdoc]|[![API Icon][SAPicon]<br/>**SAP**][sapconnector]|


## <a name="az"></a>Lista completa de A a Z

Os [detalhes do conector](/connectors/) listam os acionadores e as ações definidas no swagger, bem como os limites para cada conector.

| | | | | | | | | | | | | |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [**1**](#1) | [**A**](#a) | [**B**](#b) | [**C**](#c) | [**D**](#d) | [**E**](#e) | [**F**](#f) | [**G**](#g) | [**H**](#h) | [**I**](#i) | [**J**](#j) | [**L**](#l) | [**M**](#m) |
| [**N**](#n) | [**O**](#o) | [**P**](#p) | [**R**](#r) | [**S**](#s) | [**T**](#t) | [**U**](#u) | [**V**](#v) | [**W**](#w) | [**X**](#x) | [**Y**](#y) | [**Z**](#z) | | 

| | |
|---|---|
|<a name="1"></a>10to8 Appointment Scheduling<br/><br/><a name="a"></a>Act!<br/>Adobe Creative Cloud<br/>appFigures<br/>[AS2][as2doc]<br/>Asana<br/>Azure Active Directory (AD)<br/>API Management do Azure<br/>Serviços de Aplicações do Azure<br/>Aplicação do Azure<br/>Automatização do Azure<br/>[Armazenamento de Blobs do Azure][azureblobstoragedoc]<br/>Azure Data Lake<br/>Azure DocumentDB (Cosmos DB)<br/>[Funções do Azure][azure-functionsdoc]<br/>[Azure Logic Apps][nested-logic-appdoc]<br/>AzureML<br/>Filas do Azure<br/>Azure Resource Manager<br/>[Base de Dados SQL do Azure][sql-serverdoc]<br/><br/><a name="b"></a>Basecamp 2<br/>Basecamp 3<br/>Batch<br/>Benchmark Email<br/>Pesquisa do Bing<br/>Bitbucket<br/>Bitly<br/>BizTalk Server<br/>Blogger<br/>Box<br/>Buffer<br/><br/><a name="c"></a>Calendly<br/>Campfire<br/>Capsule CRM<br/>Chatter<br/>Cognito Forms<br/>API de Imagem Digitalizada dos Serviços Cognitivos<br/>API Face dos Serviços Cognitivos<br/>LUIS dos Serviços Cognitivos<br/>Análise de Texto dos Serviços Cognitivos<br/>Common Data Service<br/>Content Conversion<br/>Control-Terminate<br/>[APIs Personalizadas/Aplicações Web][api/web-appdoc]<br/><br/><a name="d"></a>Operações de Dados<br/>[DB2][db2doc]<br/>Disqus<br/>DocuSign<br/>Fazer Até<br/>Dropbox<br/>[Dynamics 365 CRM Online][Dynamics-365doc]<br/>Dynamics 365 for Financials<br/>Dynamics 365 for Operations<br/>Dynamics NAV<br/><br/><a name="e"></a>Easy Redmine<br/>EDIFACT<br/>[Hubs de Eventos][event-hubs-doc]<br/>Eventbrite<br/><br/><a name="f"></a>Facebook<br/>[File System][filesystemdoc]<br/>[Ficheiro Simples][flatfiledoc]<br/>FreshBooks<br/>Freshdesk<br/>Freshservice<br/>[FTP][ftpdoc]<br/><br/><a name="g"></a>GitHub<br/>Gmail<br/>Calendário Google<br/>Contactos do Google<br/>Google Drive<br/>Folhas do Google<br/>Tarefas do Google<br/>GoToMeeting<br/>GoToTraining<br/>GoToWebinar<br/><br/><a name="h"></a>Harvest<br/>HelloSign<br/>HipChat<br/>[HTTP][httpdoc]<br/>[HTTP + Swagger][http-swaggerdoc]<br/>[HTTP Webhook][webhookdoc]<br/><br/><a name="i"></a>[Informix][informixdoc]<br/>Infusionsoft<br/>Inoreader<br/>Insightly<br/>Instagram<br/>Instapaper<br/>Conta de Integração<br/>Intercom | <a name="j"></a>JotForm<br/>JIRA<br/><br/><a name="l"></a>LeanKit<br/>LiveChat<br/><br/><a name="m"></a>MailChimp<br/>Mandrill<br/>Médio<br/>Microsoft Forms<br/>Microsoft Teams<br/>Microsoft Translator<br/>[MQ][mqdoc]<br/>MSN Meteorologia<br/>Muhimbi PDF<br/>MySQL<br/><br/><a name="n"></a>Nexmo<br/><br/><a name="o"></a>[Office 365 Outlook][office365-outlookdoc]<br/>Utilizadores do Office 365<br/>Vídeos do Office 365<br/>OneDrive<br/>OneDrive para Empresas<br/>OneNote (Business)<br/>[Oracle Database][oracle-db-doc]<br/>Outlook Customer Manager<br/>Tarefas do Outlook<br/>Outlook.com<br/><br/><a name="p"></a>PagerDuty<br/>Parserr<br/>Paylocity<br/>Pinterest<br/>Pipedrive<br/>Pivotal Tracker<br/>Planner<br/>PostgreSQL<br/>Power BI<br/>Project Online<br/><br/><a name="r"></a>Redmine<br/>[Request / Response][http-requestdoc]<br/>RSS<br/><br/><a name="s"></a>[Salesforce][salesforcedoc]<br/>[SAP Application Server][sapconnector]<br/>[SAP Message Server][sapconnector]<br/>[Schedule][recurrencedoc]<br/>Âmbito<br/>SendGrid<br/>Enviar mensagens para o lote<br/>[Service Bus][service-busdoc]<br/>SFTP<br/>[SharePoint Online][sharepointdoc]<br/>[SharePoint Server][sharepointdoc]<br/>Slack<br/>Smartsheet<br/>SMTP<br/>SparkPost<br/>[SQL Server][sql-serverdoc]<br/>Stripe<br/>SurveyMonkey<br/>Switch Case<br/><br/><a name="t"></a>Teamwork Projects<br/>Teradata<br/>Todoist<br/>Toodledo<br/>[Transform XML][xmltransformdoc]<br/>Trello<br/>Twilio<br/>[Twitter][twitterdoc]<br/>Typeform<br/><br/><a name="u"></a>UserVoice<br/><br/><a name="v"></a>Variables<br/>Vimeo<br/>Visual Studio Team Services<br/><br/><a name="w"></a>WebMerge<br/>WordPress<br/>Wunderlist<br/><br/><a name="x"></a>[X12][x12doc]<br/>[XML Validation][xmlvalidatedoc]<br/><br/><a name="y"></a>Yammer<br/>YouTube<br/><br/><a name="z"></a>Zendesk |

> [!TIP]
> Para começar a utilizar o Azure Logic Apps antes de se inscrever numa conta do Azure, aceda a [Experimentar o Serviço de Aplicações Lógicas](https://tryappservice.azure.com/?appservice=logic). Pode criar de imediato uma aplicação lógica de arranque de curta duração. Sem cartões de crédito; sem compromissos.

<a id="connectors-as-triggers-and-actions" class="xliff"></a>

## Conectores como acionadores e ações

Os **acionadores** iniciam ou para instâncias da sua aplicação lógica. Alguns conectores disponibilizam acionadores que notificam a sua aplicação quando ocorrem eventos específicos. Por exemplo, o conector FTP tem o acionador `OnUpdatedFile`, que iniciar a aplicação lógica quando um ficheiro é atualizado. 

O Logic apps inclui os tipos de acionadores seguintes:  

* *Acionadores de consultas*: estes acionadores consultam o serviço a uma frequência especificada para verificar se existem novos dados. 

    Quando estão disponíveis novos dados, é executada uma nova instância da sua aplicação lógica que tem os dados como entrada. 

* *Acionadores push*: estes acionadores estão à escuta de dados num ponto final ou da ocorrência de um evento e, em seguida, acionam uma instância nova da sua aplicação lógica.

* *Acionador de periodicidade*: este acionador cria uma instância da sua aplicação lógica com base numa agenda prescrita.

Os conectores também disponibilizam **ações** que pode utilizar no seu fluxo de trabalho. Por exemplo, a sua aplicação lógica pode procurar dados e, depois, pode utilizá-los posteriormente na mesma. Mais concretamente, pode procurar dados de clientes numa base de dados SQL e utilizá-los para criar o fluxo de trabalho. 

> [!TIP]
> A[descrição geral dos conectores](connectors-overview.md) disponibiliza mais detalhes sobre os acionadores e as ações. 


<a id="message-manipulation-actions" class="xliff"></a>

## Ações de manipulação de mensagens

As aplicações lógicas incluem ações incorporadas que podem alterar ou manipular os dados do seu payload. O conector **Operações de Dados** incorporado inclui as ações seguintes: 

| | |
|---|---|
| **Compor** | Crie ou gere valores ou objetos para utilizar mais tarde ou à medida que cria o fluxo de trabalho. Por exemplo, pode criar um objeto JSON com valores de vários passos ou calcular uma constante a referenciar mais tarde numa execução de aplicação lógica. |
| **Criar tabela CSV**<br/>**Criar tabela HTML** | Transforme um conjunto de resultados de matriz numa tabela CSV ou HTML. Por exemplo, adicione a ação "Listar registos" CRM e adicione um filtro para os registos adicionados hoje. Depois, envie os resultados como uma tabela HTML por e-mail. |
| **Filtrar matriz** (consulta) | Filtre um conjunto de resultados para as entradas que lhe interessam. Por exemplo, procure todos os tweets com `#Azure` e "filtre" os tweets devolvidos para que sejam devolvidos apenas os resultados com `Tweeted_by_followers > 50`. |
| **Associar** | Associe uma matriz com um delimitador. Por exemplo, a operação Detetar Expressões-chave devolve uma matriz de expressões-chave. Pode "associá-las" com `,` ou algo parecido. Assim, em vez `["Some", "Phrase"]`, tem `"Some, Phrase"`. |
| **Analisar JSON** | Analise e aceda a valores de objetos JSON no estruturador. Por exemplo, se a sua função das Funções do Azure devolver um payload JSON, pode analisá-lo para aceder às propriedades JSON mais à frente, noutro passo. A ação também verifica se o JSON corresponde a um esquema especificado no runtime. | 
| **Selecionar** | Selecione determinadas propriedades de uma matriz para processamento adicional. Se "Listar registos" a partir do SQL e forem devolvidas 15 colunas, selecione apenas algumas destas para processamento adicional. A saída é uma matriz que só contém as propriedades que selecionar. |

<a id="custom-connectors-and-azure-certification" class="xliff"></a>

## Conectores personalizados e certificação do Azure 

Para ligar para APIs que executam o código personalizado ou não estão disponíveis como conectores, pode expandir a plataforma do Logic Apps [através da criação de Aplicações API baseadas em REST como conectores personalizados](../logic-apps/logic-apps-create-api-app.md). 

Se pretender tornar as Aplicações API personalizadas públicas e disponíveis para utilizar no Azure, submeta as suas nomeações para o [programa Microsoft Azure Certified](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/).

<a id="get-help" class="xliff"></a>

## Obter ajuda

Para fazer perguntas, responder a perguntas e ver o que os outros utilizadores do Azure Logic Apps estão a fazer, aceda ao [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ajudar a melhorar o Azure Logic Apps e os conectores, vote ou submeta ideias no [site de comentários dos utilizadores do Logic Apps](http://aka.ms/logicapps-wish).

Falta um tópico do conector ou detalhes que considera importantes? Se for o caso, ajude-nos ao adicionar aos nossos tópicos existentes ou escreva os seus próprios. A nossa documentação é open source e está alojada no GitHub. Comece no nosso [repositório do GitHub](https://github.com/Microsoft/azure-docs). 

<a id="next-steps" class="xliff"></a>

## Passos seguintes
* [Criar a sua primeira aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md)
* [Criar APIs personalizadas para aplicações lógicas](../logic-apps/logic-apps-create-api-app.md)
* [Monitorizar as aplicações lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md)

<!--Connectors Documentation-->

[api/web-appdoc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrar aplicações lógicas com Aplicações API do Serviço de Aplicações"
[azureblobstoragedoc]: ./connectors-create-api-azureblobstorage.md "Gerir ficheiros no seu contentor de blobs com o conector de armazenamento de blobs do Azure"
[azure-functionsdoc]: ../logic-apps/logic-apps-azure-functions.md "Integrar aplicações lógicas com Funções do Azure"
[db2doc]: ./connectors-create-api-db2.md "Ligue ao IBM DB2 na cloud ou no local. Atualize uma linha, obtenha uma tabela e muito mais"
[Dynamics-365doc]: ./connectors-create-api-crmonline.md "Ligue ao Dynamics CRM Online para que possa trabalhar com os seus dados do CRM Online"
[event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Ligue aos Hubs de Eventos do Azure. Receba e envie eventos entre aplicações lógicas e os Hubs de Eventos"
[filesystemdoc]: ../logic-apps/logic-apps-using-file-connector.md "Ligue a um sistema de ficheiros no local"
[ftpdoc]: ./connectors-create-api-ftp.md "Ligue a um servidor FTP / FTPS e para tarefas de FTP, como carregar, obter, eliminar ficheiros, entre outras"
[httpdoc]: ./connectors-native-http.md "Faça chamadas HTTP com o conector HTTP"
[http-requestdoc]: ./connectors-native-reqres.md "Adicione ações para pedidos HTTP e respostas para as suas aplicações lógicas"
[http-swaggerdoc]: ./connectors-native-http-swagger.md "Faça chamadas HTTP com o conector HTTP + Swagger."
[informixdoc]: ./connectors-create-api-informix.md "Ligue o Informix na cloud ou no local. Leia uma linha, liste as tabelas e muito mais"
[nested-logic-appdoc]: ../logic-apps/logic-apps-http-endpoint.md "Integre aplicações lógicas com fluxos de trabalho aninhados"
[office365-outlookdoc]: ./connectors-create-api-office365-outlook.md "Ligue à sua conta do Office 365. Envie e receba e-mails, gira o seu calendário e contactos, e muito mais"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Ligue a uma base de dados Oracle para adicionar, inserir, eliminar linhas e mais"
[mqdoc]: ./connectors-create-api-mq.md "Ligar ao MQ no local ou ao Azure e enviar e receber mensagens"
[recurrencedoc]:  ./connectors-native-recurrence.md "Acione ações periódicas para aplicações lógicas"
[salesforcedoc]: ./connectors-create-api-salesforce.md "Ligue à sua conta do Salesforce. Gira contas, responsáveis, oportunidades e muito mais"
[sapconnector]: ../logic-apps/logic-apps-using-sap-connector.md "Ligue a um sistema SAP no local"
[service-busdoc]: ./connectors-create-api-servicebus.md "Envie mensagens das Filas e dos Tópicos do Service Bus e receba mensagens das Filas e das Subscrições do Service Bus"
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Ligue ao SharePoint Online. Gira documentos, liste itens e muito mais"
[sql-serverdoc]: ./connectors-create-api-sqlazure.md "Ligue à Base de Dados SQL do Azure ou ao SQL Server. Crie, atualize, obtenha e elimine entradas numa tabela da Base de Dados SQL."
[twitterdoc]: ./connectors-create-api-twitter.md "Ligue ao Twitter. Obtenha linhas cronológicas, publique tweets e muito mais"
[webhookdoc]: ./connectors-native-webhook.md "Adicione ações e acionadores de Webhook às suas aplicações lógicas"

[as2doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Saiba mais sobre a integração empresarial AS2."
[x12doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Saiba mais sobre a integração empresarial X12."
[flatfiledoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Saiba mais sobre a integração empresarial de ficheiros simples."
[flatfiledecodedoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Saiba mais sobre a integração empresarial de ficheiros simples."
[xmlvalidatedoc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Saiba mais sobre a integração empresarial de validação XML."
[xmltransformdoc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Saiba mais sobre a integração empresarial de transformações."
[as2decode]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "Saiba mais sobre a integração empresarial de descodificação AS2."
[as2encode]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "Saiba mais sobre a integração empresarial de codificação AS2."
[X12decode]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Saiba mais sobre a integração empresarial de descodificação X12."
[X12encode]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Saiba mais sobre a integração empresarial de codificação X12."
[EDIFACTdecode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Saiba mais sobre a integração empresarial de descodificação EDIFACT."
[EDIFACTencode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Saiba mais sobre a integração empresarial de codificação EDIFACT."
[integrationaccountdoc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Procure esquemas, mapas, parceiros e mais na sua conta de integração"


[boxDoc]: ./connectors-create-api-box.md "Ligue à Caixa. Carregue, obtenha, elimine, liste os seus ficheiros e muito mais"
[delaydoc]: ./connectors-native-delay.md "Efetue ações atrasadas"
[dropboxdoc]: ./connectors-create-api-dropbox.md "Ligue à Dropbox. Carregue, obtenha, elimine, liste os seus ficheiros e muito mais"
[facebookdoc]: ./connectors-create-api-facebook.md "Ligue ao Facebook. Publique numa linha cronológica, obtenha um feed de página, entre outras coisas"
[githubdoc]: ./connectors-create-api-github.md "Ligue o GitHub e controle problemas"
[google-drivedoc]: ./connectors-create-api-googledrive.md "Ligue ao GoogleDrive para que possa trabalhar com os seus dados"
[google-sheetsdoc]: ./connectors-create-api-googlesheet.md "Ligue ao Planilhas Google, para que possa modificar as suas folhas"
[google-tasksdoc]: ./connectors-create-api-googletasks.md "Ligue o Google Tarefas para que possa gerir as suas tarefas"
[google-calendardoc]: ./connectors-create-api-googlecalendar.md "Liga ao Calendário Google e pode gerir o calendário."
[http-responsedoc]: ./connectors-native-reqres.md "Adicione ações para pedidos HTTP e respostas para as suas aplicações lógicas"
[instagramdoc]: ./connectors-create-api-instagram.md "Ligue ao Instagram. Acione ou interaja em eventos"
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "Ligue à sua conta do MailChimp. Gira e automatize e-mails"
[mandrilldoc]: ./connectors-create-api-mandrill.md "Ligue ao Mandrill para comunicação"
[microsoft-translatordoc]: ./connectors-create-api-microsofttranslator.md "Ligue-se ao Microsoft Translator. Traduza texto, detete idiomas e muito mais" 
[office365-usersdoc]: ./connectors-create-api-office365-users.md 
[office365-videodoc]: ./connectors-create-api-office365-video.md "Obtenha informações de vídeo, listas de vídeos e canais, e URLs de reprodução para vídeos do Office 365"
[onedrivedoc]: ./connectors-create-api-onedrive.md "Ligue ao seu Microsoft OneDrive pessoal. Carregue, elimine, liste os ficheiros e muito mais"
[onedrive-for-businessdoc]: ./connectors-create-api-onedriveforbusiness.md "Ligue ao seu Microsoft OneDrive empresarial. Carregue, elimine, liste os seus ficheiros e muito mais"
[outlook.comdoc]: ./connectors-create-api-outlook.md "Ligue à sua caixa de correio do Outlook. Gira o seu e-mail, calendários, contactos e muito mais"
[project-onlinedoc]: ./connectors-create-api-projectonline.md "Ligue-se ao Microsoft Project Online. Gira os seus projetos, tarefas, recursos e muito mais"
[querydoc]: ./connectors-native-query.md "Selecione e filtre matrizes com a ação Consultar"
[rssdoc]: ./connectors-create-api-rss.md "Publique e obtenha itens de feeds, acione operações quando um novo item é publicado num feed RSS."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Ligue ao SendGrid. Envie e-mails e gira listas de recipientes"
[sftpdoc]: ./connectors-create-api-sftp.md "Ligue à sua conta do SFTP. Carregue, obtenha, elimine ficheiros e muito mais"
[slackdoc]: ./connectors-create-api-slack.md "Ligue ao Slack e publica mensagens nos canais do Slack"
[smtpdoc]: ./connectors-create-api-smtp.md "Ligue a um servidor SMTP e envie e-mails com anexos"
[sparkpostdoc]: ./connectors-create-api-sparkpost.md "Ligue ao SparkPost para comunicação"
[trellodoc]: ./connectors-create-api-trello.md "Ligue ao Trello. Gira os seus projetos e organize tudo com qualquer pessoa"
[twiliodoc]: ./connectors-create-api-twilio.md "Ligue ao Twilio. Envie e receba mensagens, obtenha números disponíveis, gira números de telefone de chamadas recebidas, entre outras ações"
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Ligue ao Wunderlist. Gira tarefas e listas de itens pendentes, mantenha a sua vida em sincronização e muito mais"
[yammerdoc]: ./connectors-create-api-yammer.md "Ligue ao Yammer. Publique mensagens, obtenha novas mensagens e muito mais"
[youtubedoc]: ./connectors-create-api-youtube.md "Ligue ao YouTube. Gira os seus vídeos e canais"


<!--Icon references-->
[appFiguresicon]: ./media/apis-list/appfigures.png
[Asanaicon]: ./media/apis-list/asana.png
[Azure-Automation-icon]: ./media/apis-list/azure-automation.png
[AzureBlobStorageicon]: ./media/apis-list/azureblob.png
[Azure-Data-Lake-icon]: ./media/apis-list/azure-data-lake.png
[Azure-DocumentDBicon]: ./media/apis-list/azure-documentdb.png
[Azure-MLicon]: ./media/apis-list/azureml.png
[Azure-Resource-Manager-icon]: ./media/apis-list/azure-resource-manager.png
[Azure-Queues-icon]: ./media/apis-list/azure-queues.png
[Basecamp-3icon]: ./media/apis-list/basecamp.png
[Bitbucket-icon]: ./media/apis-list/bitbucket.png
[Bitlyicon]: ./media/apis-list/bitly.png
[BizTalk-Servericon]: ./media/apis-list/biztalk.png
[Bloggericon]: ./media/apis-list/blogger.png
[Boxicon]: ./media/apis-list/box.png
[Campfireicon]: ./media/apis-list/campfire.png
[Cognitive-Services-Text-Analyticsicon]: ./media/apis-list/cognitiveservicestextanalytics.png
[DB2icon]: ./media/apis-list/db2.png
[Dropboxicon]: ./media/apis-list/dropbox.png
[Dynamics-365icon]: ./media/apis-list/dynamicscrmonline.png
[Dynamics-365-for-Financialsicon]: ./media/apis-list/madeira.png
[Dynamics-365-for-Operationsicon]: ./media/apis-list/dynamicsax.png
[Easy-Redmineicon]: ./media/apis-list/easyredmine.png
[Event-Hubs-icon]: ./media/apis-list/eventhubs.png
[Facebookicon]: ./media/apis-list/facebook.png
[FTPicon]: ./media/apis-list/ftp.png
[GitHubicon]: ./media/apis-list/github.png
[Google-Calendaricon]: ./media/apis-list/googlecalendar.png
[Google-Driveicon]: ./media/apis-list/googledrive.png
[Google-Sheetsicon]: ./media/apis-list/googlesheet.png
[Google-Tasksicon]: ./media/apis-list/googletasks.png
[HideKeyicon]: ./media/apis-list/hidekey.png
[HipChaticon]: ./media/apis-list/hipchat.png
[Informixicon]: ./media/apis-list/informix.png
[Insightlyicon]: ./media/apis-list/insightly.png
[Instagramicon]: ./media/apis-list/instagram.png
[Instapapericon]: ./media/apis-list/instapaper.png
[JIRAicon]: ./media/apis-list/jira.png
[MailChimpicon]: ./media/apis-list/mailchimp.png
[Mandrillicon]: ./media/apis-list/mandrill.png
[Microsoft-Translatoricon]: ./media/apis-list/microsofttranslator.png
[MQicon]: ./media/apis-list/mq.png
[Office-365-Outlookicon]: ./media/apis-list/office365.png
[Office-365-Usersicon]: ./media/apis-list/office365users.png
[Office-365-Videoicon]: ./media/apis-list/office365video.png
[OneDriveicon]: ./media/apis-list/onedrive.png
[OneDrive-for-Businessicon]: ./media/apis-list/onedriveforbusiness.png
[Oracle-DB-icon]: ./media/apis-list/oracle-db.png
[Outlook.comicon]: ./media/apis-list/outlook.png
[PagerDutyicon]: ./media/apis-list/pagerduty.png
[Pinteresticon]: ./media/apis-list/pinterest.png
[Project-Onlineicon]: ./media/apis-list/projectonline.png
[Redmineicon]: ./media/apis-list/redmine.png
[RSSicon]: ./media/apis-list/rss.png
[Common-Data-Serviceicon]: ./media/apis-list/runtimeservice.png
[Salesforceicon]: ./media/apis-list/salesforce.png
[SAPicon]: ./media/apis-list/sap.png
[SendGridicon]: ./media/apis-list/sendgrid.png
[Service-Busicon]: ./media/apis-list/servicebus.png
[SFTPicon]: ./media/apis-list/sftp.png
[SharePointicon]: ./media/apis-list/sharepointonline.png
[Slackicon]: ./media/apis-list/slack.png
[Smartsheeticon]: ./media/apis-list/smartsheet.png
[SMTPicon]: ./media/apis-list/smtp.png
[SparkPosticon]: ./media/apis-list/sparkpost.png
[SQL-Servericon]: ./media/apis-list/sql.png
[Todoisticon]: ./media/apis-list/todoist.png
[Trelloicon]: ./media/apis-list/trello.png
[Twilioicon]: ./media/apis-list/twilio.png
[Twittericon]: ./media/apis-list/twitter.png
[Vimeoicon]: ./media/apis-list/vimeo.png
[Visual-Studio-Team-Servicesicon]: ./media/apis-list/visualstudioteamservices.png
[WordPressicon]: ./media/apis-list/wordpress.png
[Wunderlisticon]: ./media/apis-list/wunderlist.png
[Yammericon]: ./media/apis-list/yammer.png
[YouTubeicon]: ./media/apis-list/youtube.png

<!-- Primitive Icons -->
[API/Web-Appicon]: ./media/apis-list/api.png
[Azure-Functionsicon]: ./media/apis-list/function.png
[Delayicon]: ./media/apis-list/delay.png
[FileSystemIcon]: ./media/apis-list/filesystem.png
[HTTPicon]: ./media/apis-list/http.png
[HTTP-Requesticon]: ./media/apis-list/request.png
[HTTP-Responseicon]: ./media/apis-list/response.png
[HTTP-Swaggericon]: ./media/apis-list/http_swagger.png
[Nested-Logic-Appicon]: ./media/apis-list/workflow.png
[Recurrenceicon]: ./media/apis-list/recurrence.png
[Queryicon]: ./media/apis-list/query.png
[Webhookicon]: ./media/apis-list/webhook.png

<!-- EIP Icons -->
[as2icon]: ./media/apis-list/as2.png
[x12icon]: ./media/apis-list/x12new.png
[flatfileicon]: ./media/apis-list/flatfileencoding.png
[flatfiledecodeicon]: ./media/apis-list/flatfiledecoding.png
[xmlvalidateicon]: ./media/apis-list/xmlvalidation.png
[xmltransformicon]: ./media/apis-list/xsltransform.png
[integrationaccounticon]: ./media/apis-list/integrationaccount.png

