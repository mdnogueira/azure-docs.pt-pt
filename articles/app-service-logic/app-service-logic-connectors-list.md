<properties
    pageTitle="Lista de Conectores e API Apps disponíveis | App Service do Microsoft Azure"
    description="Leia sobre os Conectores e API Apps no App Service do Azure e"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/28/2016"
    ms.author="mandia"/>


# Lista de Conectores e API Apps para utilizar nas suas Logic Apps
>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema de pré-visualização 2014-12-01 das Logic Apps. Para a versão de Disponibilidade Geral (GA) das Logic Apps, veja a [Nova Lista de Conectores](../connectors/apis-list.md).

Saiba mais sobre todos os conectores e API Apps disponíveis criados pela Microsoft para utilizar nas suas Logic Apps.

Para obter informações sobre preços e uma lista do que está incluído com cada Escalão de Serviços, consulte [Preços dos Serviços de Aplicações do Azure](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Se pretende uma introdução às Logic Apps antes de se inscrever para uma conta do Azure, aceda a [Experimentar a Aplicação Lógica](https://tryappservice.azure.com/?appservice=logic). Pode criar de imediato uma aplicação lógica de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.

## Conectores de Núcleo
A tabela seguinte apresenta uma lista de todos os conectores e API Apps disponíveis criados pela Microsoft que estão disponíveis como Conectores de Núcleo:

Nome | Descrição
--- | ---
[Service Bus do Azure](app-service-logic-connector-azureservicebus.md) | Pode enviar mensagens de Filas e Tópicos do Service Bus e receber mensagens de Filas e Subscrições do Service Bus.
[Bing Translator](https://azure.microsoft.com/marketplace/partners/microsoft_com/bingtranslator) | Utilize o Bing para traduzir texto para outro idioma.
[HTTP](app-service-logic-connector-http.md) | O Serviço de Escuta de HTTP abre um ponto final que age como um servidor HTTP e escuta pedidos HTTP ou HTTPS recebidos. A ação de HTTP não necessita de uma Aplicação API e é suportada nativamente em Logic Apps.
[Microsoft Office 365](app-service-logic-connector-office365.md) | O conector do Office 365 pode enviar e receber e-mails, gerir o seu calendário e gerir os seus contactos com a sua conta do Office 365.
[QuickBooks](app-service-logic-connector-quickbooks.md) | Pode concluir tarefas diferentes, incluindo a criação, atualização e consulta de entidades diferentes a partir do Intuit QuickBooks como os clientes, faturas, itens e assim sucessivamente.
[Slack](app-service-logic-connector-slack.md) | Ligue-se ao Slack e publique mensagens nos canais Slack.
[Wait](app-service-logic-connector-wait.md) | Utilize este conector para atrasar a execução da sua aplicação. Pode atrasar a aplicação durante um período específico ou até uma ocorrência num momento específico.


## Conectores de Integração do Enterprise
A tabela seguinte apresenta uma lista de todos os Conectores e API Apps disponíveis criados pela Microsoft que estão disponíveis como Conectores de Integração do Enterprise:

Nome  | Descrição
------------- | -------------
[Conector AS2](app-service-logic-connector-as2.md) | Pode receber e enviar mensagens utilizando o protocolo de transporte AS2. Os dados são transportados de forma segura e fiável através da utilização de certificados digitais e encriptação.
[EDIFACT de BizTalk](app-service-logic-connector-edifact.md) | Receba e envie mensagens utilizando o protocolo EDIFACT nas comunicações empresa-empresa.
[Codificador de Ficheiro Simples de BizTalk](app-service-logic-flatfile-encoder.md) | Fornece a interoperabilidade entre dados de ficheiro simples (tal como excel e csv) e dados XML. Esta Aplicação API pode converter uma instância do ficheiro simples em XML e vice versa.
[Codificador de BizTalk JSON](app-service-logic-connector-jsonencoder.md) | Um codificador e descodificador que o ajuda a sua aplicação na interoperabilidade entre dados JSON e XML. Pode converter uma determinada instância JSON em XML e vice-versa.
[Regras de BizTalk](app-service-logic-use-biztalk-rules.md) | Utilize As Regras de BizTalk para definir e controlar a lógica de negócio numa organização. As políticas de negócio podem ser atualizadas sem recompilar ou sem voltar a implementar as aplicações associadas.
[Gestão de Parceiros Comerciais de BizTalk](app-service-logic-connector-tpm.md) | Define e persiste relações empresa-empresa utilizando parceiros, contratos, esquemas e certificados utilizados nos contratos. Estas relações são aplicadas utilizando as API Apps AS2, EDIFACT e X12.
[Serviço de Transformação de BizTalk](app-service-logic-transform-xml-documents.md) | Converte dados de um formato para outro formato. Também pode carregar um mapa existente (ficheiro .trfm), ver as ligações entre os esquemas de origem e de destino e utilizar a funcionalidade de 'Teste' com exemplos de conteúdo de entrada de XML. Também estão disponíveis diferentes funções incorporadas incluindo manipulações de cadeia, atribuição condicional e muito mais.
[X12 de BizTalk](app-service-logic-connector-x12.md) | Receba e envie mensagens utilizando o protocolo X12 nas comunicações empresa-empresa.
[Validação XML do BizTalk](app-service-logic-xml-validator.md) | Valida dados XML em relação a esquemas XML predefinidos. Pode utilizar esquemas existentes ou gerar esquemas com base numa instância do ficheiro simples, instância JSON, ou conectores existentes.
[Extrator XPath de BizTalk](app-service-logic-xpath-extract.md) | Procura e extrai dados a partir do conteúdo XML com base num XPath que escolher.
[Conector DB2](app-service-logic-connector-db2.md) | Estabelece ligação a uma base de dados IBM DB2 no local e numa máquina virtual do Azure em execução num sistema operativo Windows. É possível mapear operações API Web e API OData para comandos Informix Structured Query Language. <br/><br/>Nenhum acionador. As ações incluem selecionar, inserir, atualizar, eliminar tabela e declaração personalizada<br/><br/>Este conector também inclui o Cliente Microsoft para DRDA para ligar a um servidor Informix através de uma rede TCP/IP.
[Ficheiro](app-service-logic-connector-file.md) | Ao utilizar este conector, pode ligar ao sistema de ficheiros no local ou à rede e concluir tarefas de ficheiro diferentes, incluindo carregar, eliminar, listagem de ficheiros e muito mais.
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | Estabelece ligação a um servidor FTP/FTPS e realiza diferentes tarefas FTP, incluindo carregar, obter, eliminar ficheiros, entre outros.
[Informix](app-service-logic-connector-informix.md) | Estabelece ligação a uma base de dados IBM Informix no local e numa máquina virtual do Azure em execução num sistema operativo Windows. É possível mapear operações API Web e API OData para comandos Informix Structured Query Language.<br/><br/>Nenhum acionador. As ações incluem selecionar, inserir, atualizar, eliminar tabela e declaração personalizada.<br/><br/>Se utilizar no local, podem ser utilizadas a VPN ou Azure ExpressRoute. Este conector também inclui o Cliente Microsoft para DRDA para ligar a um servidor Informix através de uma rede TCP/IP.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Estabelece ligação ao SQL Server no local ou a uma SQL Database do Azure. Pode criar, atualizar, obter e eliminar entradas numa tabela de SQL Database.
MQ | Estabelece ligação à versão 8 do IBM WebSphere MQ Server no local e numa máquina virtual do Azure em execução num sistema operativo Windows. Se utilizar no local, podem ser utilizadas a VPN ou Azure ExpressRoute. O conector também inclui o Cliente Microsoft para MQ.<br/><br/>Nenhum acionador. Nenhuma ação.<br/><br/>**Nota** Atualmente não pode ser utilizado com Logic Apps.
[Base de dados Oracle](app-service-logic-connector-oracle.md) | Estabelece ligação à Base de Dados Oracle no local e pode criar, atualizar, obter e eliminar entradas numa tabela de base de dados.
[POP3](app-service-logic-connector-pop3.md) (Post Office Protocol)| Liga a um servidor POP3 para obter mensagens de correio eletrónico com anexos.
[SAP](app-service-logic-connector-sap.md) | Liga a um servidor SAP no local e invoca RFCs, BAPIs e tRFCs e envia IDOCs.

## Conectores como Acionadores
Vários conectores fornecem acionadores para Logic Apps. Estes acionadores são de dois tipos:

1. Acionadores de Consultas: estes acionadores consultam o serviço a uma frequência especificada para verificar se existem novos dados. Quando estão disponíveis novos dados, é executada uma nova instância da sua Aplicação Lógica com a entrada desses dados. Para impedir que os mesmos dados sejam consumidos várias vezes, o acionador pode limpar os dados que foram lidos e transmitidos para a sua Aplicação Lógica. Exemplos de tais conectores são Ficheiro, SQL e Storage do Azure.
2. Acionadores Push: estes acionadores estão à escuta de dados num ponto final ou da ocorrência de um evento. Em seguida, acionam uma nova instância de uma Aplicação Lógica. Exemplos de tais conectores são o Serviço de Escuta de HTTP e Twitter.

## Conectores como Ações
Os conectores também podem ser utilizados como ações na sua Aplicação Lógica. As ações são úteis para procurar dados na sua Aplicação Lógica que podem ser utilizados na execução. Por exemplo, pode precisar de procurar dados de numa SQL Database para obter mais informações sobre um cliente durante o processamento de uma ordem. Alternativamente, pode ter de escrever, atualizar ou eliminar dados num destino. Pode fazê-lo utilizando as ações fornecidas pelos conectores. As ações mapeiam para operações em API Apps (conforme definido pelos respetivos metadados Swagger).

## Criar os seus próprios Conectores e API Apps
[Referência de Conectores e API Apps](http://aka.ms/appservicesconnectorreference)  
[Acionadores da Aplicação API do App Service do Azure](../app-service-api/app-service-api-dotnet-triggers.md)  
[Referência de Aplicação Lógica](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## Obter mais informações sobre Conectores e API Apps
[O que são Conectores e API Apps BizTalk](app-service-logic-what-are-biztalk-api-apps.md)  
[Utilizar o Gestor de Ligações Híbridas no App Service do Azure](app-service-logic-hybrid-connection-manager.md)  
[Gerir e Monitorizar as suas API Apps e Conectores incorporados](app-service-logic-monitor-your-connectors.md)



<!--HONumber=Aug16_HO1-->


