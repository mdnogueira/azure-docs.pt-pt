---
title: "Lista de Conectores e API Apps disponíveis | Microsoft Docs"
description: Leia sobre os Conectores e API Apps no App Service do Azure e
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: cgronlun
ms.assetid: 984a425d-ba64-48cc-90dc-bb624411e0f0
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6028fc53661978f63bbac89be6214ffebdc22405


---
# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>Lista de Conectores e API Apps para utilizar nas suas Logic Apps
> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema de pré-visualização 2014-12-01 das Logic Apps. Para a versão de Disponibilidade Geral (GA) das Logic Apps, veja a [Nova Lista de Conectores](../connectors/apis-list.md).
> 
> 

Saiba mais sobre todos os conectores e API Apps disponíveis criados pela Microsoft para utilizar nas suas Logic Apps.

Para obter informações sobre preços e uma lista do que está incluído com cada Escalão de Serviços, consulte [Preços dos Serviços de Aplicações do Azure](https://azure.microsoft.com/pricing/details/app-service/).

> [!NOTE]
> Se pretende uma introdução às Logic Apps antes de se inscrever para uma conta do Azure, aceda a [Experimentar a Aplicação Lógica](https://tryappservice.azure.com/?appservice=logic). Pode criar de imediato uma aplicação lógica de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.
> 
> 

## <a name="core-connectors"></a>Conectores de Núcleo
A tabela seguinte apresenta uma lista de todos os conectores e API Apps disponíveis criados pela Microsoft que estão disponíveis como Conectores de Núcleo:

| Nome | Descrição |
| --- | --- |
| [Bing Tradutor](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) |Utilize o Bing para traduzir texto para outro idioma. |
| [HTTP](app-service-logic-connector-http.md) |O Serviço de Escuta de HTTP abre um ponto final que age como um servidor HTTP e escuta pedidos HTTP ou HTTPS recebidos. A ação de HTTP não necessita de uma Aplicação API e é suportada nativamente em Logic Apps. |
| [Slack](app-service-logic-connector-slack.md) |Ligue-se ao Slack e publique mensagens nos canais Slack. |

## <a name="enterprise-integration-connectors"></a>Conectores de Integração do Enterprise
A tabela seguinte apresenta uma lista de todos os Conectores e API Apps disponíveis criados pela Microsoft que estão disponíveis como Conectores de Integração do Enterprise:

| Nome | Descrição |
| --- | --- |
| [Regras de BizTalk](app-service-logic-use-biztalk-rules.md) |Utilize As Regras de BizTalk para definir e controlar a lógica de negócio numa organização. As políticas de negócio podem ser atualizadas sem recompilar ou sem voltar a implementar as aplicações associadas. |
| [Extrator XPath de BizTalk](app-service-logic-xpath-extract.md) |Procura e extrai dados a partir do conteúdo XML com base num XPath que escolher. |
| [Conector DB2](app-service-logic-connector-db2.md) |Estabelece ligação a uma base de dados IBM DB2 no local e numa máquina virtual do Azure em execução num sistema operativo Windows. É possível mapear operações API Web e API OData para comandos Informix Structured Query Language. <br/><br/>Nenhum acionador. As ações incluem selecionar, inserir, atualizar, eliminar tabela e declaração personalizada<br/><br/>Este conector também inclui o Cliente Microsoft para DRDA para ligar a um servidor Informix através de uma rede TCP/IP. |
| [Ficheiro](app-service-logic-connector-file.md) |Ao utilizar este conector, pode ligar ao sistema de ficheiros no local ou à rede e concluir tarefas de ficheiro diferentes, incluindo carregar, eliminar, listagem de ficheiros e muito mais. |
| [Informix](app-service-logic-connector-informix.md) |Estabelece ligação a uma base de dados IBM Informix no local e numa máquina virtual do Azure em execução num sistema operativo Windows. É possível mapear operações API Web e API OData para comandos Informix Structured Query Language.<br/><br/>Nenhum acionador. As ações incluem selecionar, inserir, atualizar, eliminar tabela e declaração personalizada.<br/><br/>Se utilizar no local, podem ser utilizadas a VPN ou Azure ExpressRoute. Este conector também inclui o Cliente Microsoft para DRDA para ligar a um servidor Informix através de uma rede TCP/IP. |
| [Microsoft SQL Server](app-service-logic-connector-sql.md) |Estabelece ligação ao SQL Server no local ou a uma SQL Database do Azure. Pode criar, atualizar, obter e eliminar entradas numa tabela de SQL Database. |
| MQ |Estabelece ligação à versão 8 do IBM WebSphere MQ Server no local e numa máquina virtual do Azure em execução num sistema operativo Windows. Se utilizar no local, podem ser utilizadas a VPN ou Azure ExpressRoute. O conector também inclui o Cliente Microsoft para MQ.<br/><br/>Nenhum acionador. Nenhuma ação.<br/><br/>**Nota** Atualmente não pode ser utilizado com Logic Apps. |

## <a name="connectors-as-triggers"></a>Conectores como Acionadores
Vários conectores fornecem acionadores para Logic Apps. Estes acionadores são de dois tipos:

1. Acionadores de Consultas: estes acionadores consultam o serviço a uma frequência especificada para verificar se existem novos dados. Quando estão disponíveis novos dados, é executada uma nova instância da sua Aplicação Lógica com a entrada desses dados. Para impedir que os mesmos dados sejam consumidos várias vezes, o acionador pode limpar os dados que foram lidos e transmitidos para a sua Aplicação Lógica. Exemplos de tais conectores são Ficheiro, SQL e Storage do Azure.
2. Acionadores Push: estes acionadores estão à escuta de dados num ponto final ou da ocorrência de um evento. Em seguida, acionam uma nova instância de uma Aplicação Lógica. Exemplos de tais conectores são o Serviço de Escuta de HTTP e Twitter.

## <a name="connectors-as-actions"></a>Conectores como Ações
Os conectores também podem ser utilizados como ações na sua Aplicação Lógica. As ações são úteis para procurar dados na sua Aplicação Lógica que podem ser utilizados na execução. Por exemplo, pode precisar de procurar dados de numa SQL Database para obter mais informações sobre um cliente durante o processamento de uma ordem. Alternativamente, pode ter de escrever, atualizar ou eliminar dados num destino. Pode fazê-lo utilizando as ações fornecidas pelos conectores. As ações mapeiam para operações em API Apps (conforme definido pelos respetivos metadados Swagger).

## <a name="create-your-own-connectors-and-api-apps"></a>Criar os seus próprios Conectores e API Apps
[Referência de Conectores e Aplicações API](http://aka.ms/appservicesconnectorreference)  
[Acionadores da aplicação API do Serviço de Aplicações do Azure](../app-service-api/app-service-api-dotnet-triggers.md)  
[Referência de Aplicação Lógica](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>Obter mais informações sobre Conectores e API Apps
[O que são Conectores e Aplicações API do BizTalk](app-service-logic-what-are-biztalk-api-apps.md)  
[Utilizar o Gestor de Ligações Híbridas no Serviço de Aplicações do Azure](app-service-logic-hybrid-connection-manager.md)  
[Gerir e Monitorizar as suas Aplicações API e Conectores incorporados](app-service-logic-monitor-your-connectors.md)




<!--HONumber=Nov16_HO2-->


