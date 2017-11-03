---
title: "Mover as aplicações dos BizTalk Services para aplicações lógicas do Azure | Microsoft Docs"
description: "Mover ou migrar MABS de serviços do BizTalk do Azure para aplicações lógicas"
services: logic-apps
documentationcenter: 
author: jonfancey
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: ladocs; jonfan; mandia
ms.openlocfilehash: df26e4669158e5aa9e3b9a7af888d0dbbba273dd
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="move-from-biztalk-services-to-logic-apps"></a>Mover dos BizTalk Services para aplicações lógicas

Extinguir o serviços de BizTalk do Azure (MABS) da Microsoft. Utilize este tópico para mover as soluções de integração MABS para Azure Logic Apps. 

## <a name="overview"></a>Descrição geral

BizTalk Services é composta por dois serviços secundárias:

1.  Microsoft BizTalk Services ligações híbridas
2.  Integração com base em bridge EAI e EDI

Se estiver à procura de mover as ligações híbridas, em seguida, [as ligações híbridas do serviço de aplicação do Azure](../app-service/app-service-hybrid-connections.md) descreve as alterações e funcionalidades deste serviço. Azure ligações híbridas substitui as ligações do BizTalk Services híbridas. Ligações de híbrida do Azure está disponível com o App Service do Azure e é fornecida no portal do Azure. Ligações de híbrida do Azure também fornece um novo Gestor de ligações híbridas para gerir as ligações híbridas do BizTalk Services existentes e novas ligações híbridas que cria no portal. Ligações híbridas do serviço de aplicação do Azure é geralmente disponível (GA).

EAI e EDI com base em bridge integração, as Logic Apps é a substituição. As Logic Apps fornece as mesmas capacidades do que os BizTalk Services e muito mais. As Logic Apps fornece o fluxo de trabalho de com base no consumo escala da nuvem e as funcionalidades de orquestração que permitem-lhe de forma rápida e facilmente criar soluções de integração complexos utilizando um browser, ou ferramentas no Visual Studio.

A tabela seguinte fornece um mapeamento das capacidades do BizTalk Services para aplicações lógicas.

| Serviços BizTalk   | Aplicações Lógicas            | Objetivo                  |
| ------------------ | --------------------- | ---------------------------- |
| conector          | conector             | Enviar e receber dados   |
| Bridge             | Aplicação Lógica             | Processador de pipeline           |
| Validar fase     | Ação de validação de XML      | Validar um documento XML com um esquema             |
| Enriqueça a fase       | Tokens de dados      | Promova propriedades em mensagens de ou para tomar decisões de encaminhamento             |
| Fase de transformação    | Ação de transformação      | Converter mensagens XML de um formato para outro             |
| Descodificar fase       | Ação de descodificar o ficheiro simples      | Converter a partir do ficheiro simples em XML             |
| Fase de codificar       |  Ação de codificar o ficheiro simples      | Converter a partir de XML para ficheiros simples             |
| Mensagem Inspector       |  As funções do Azure ou das API Apps      | Executar código personalizado no seu integrações             |
| Ação de rota      |  Condição ou comutador      | Encaminhar mensagens para um dos conectores especificados             |

Há uma série de diferentes tipos de artefacto nos BizTalk Services.

## <a name="connectors"></a>Conectores
Conectores no BizTalk Services permitem pontes enviar e receber dados, incluindo bidirecionais pontes que ativado interações de pedido/resposta baseado em HTTP. As Logic Apps, é utilizada a terminologia do mesma. Conectores nas logic apps servir a mesma finalidade e também incluir mais 140 que possam ligar a uma matriz abrangente de tecnologias e serviços no local utilizando o Gateway de dados no local (substituir o BizTalk Adapter Service utilizados pelo BizTalk Services), e na nuvem serviços SaaS e PaaS, como o OneDrive, Office 365, Dynamics CRM e muitos mais.

Origens nos BizTalk Services estão limitadas a subscrição de FTP, SFTP e fila do Service Bus ou tópico.

![](media/logic-apps-move-from-mabs/sources.png)

Cada bridge tem um ponto final de HTTP, por predefinição, o que está configurada com o endereço de tempo de execução e as propriedades de endereço relativo da bridge. Para alcançar os mesmos com Logic Apps, utilize o [pedido e resposta](../connectors/connectors-native-reqres.md) ações.

## <a name="xml-processing-and-bridges"></a>Processamento de XML e bridges
Uma ponte nos BizTalk Services é semelhante a um pipeline de processamento. Uma ponte pode demorar recebidos a partir de um conector de dados, e algumas trabalhar com os dados e, em seguida, envia-as para outro sistema. As Logic Apps tem a mesma ao suportar os padrões de interação com base no pipeline mesmo como os BizTalk Services e também fornece um número de outros padrões de integração. O [Bridge de pedido-resposta XML](https://msdn.microsoft.com/library/azure/hh689781.aspx) nos BizTalk Services é conhecido como um pipeline VETER constituídas por fases que podem:

* (V) validar
* (E) enriquecer a
* (T) transformação
* (E) enriquecer a
* (R) rota

Como mostrado na imagem seguinte, o processamento é dividido entre pedido e resposta e permite um controlo sobre o pedido e os caminhos de resposta separadamente (por exemplo, utilizando mapas diferentes para cada):

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Além disso, um XML bridge unidirecional adiciona fases Decode e codificar no início e fim do processamento e a bridge de pass-through contém uma fase de Enrich único.

### <a name="message-processing-and-decodingencoding"></a>Descodificar/codificação e processamento de mensagens
Nos BizTalk Services, pode recebe mensagens XML de diferentes tipos e determinar o esquema correspondente para a mensagem recebida. Este procedimento é efetuado no **tipos de mensagens** fase de pipeline de processamento de receção. Em seguida, a fase de Decode utiliza o tipo de mensagem detetado descodificá-la utilizando o esquema fornecido. Se o esquema é um esquema de flatfile, converte-a entrada flatfile XML. 

As Logic Apps fornece capacidades semelhantes. Receber uma flatfile através de um sem-número de protocolos diferentes, utilizando os acionadores de conector diferente (sistema de ficheiros, FTP, HTTP e assim sucessivamente) e utilizar o [descodificar o ficheiro simples](../logic-apps/logic-apps-enterprise-integration-flatfile.md) ação para converter os dados de entrada em XML. Pode mover as esquemas de ficheiro simples existente diretamente para as logic apps sem necessidade de alterações e, em seguida, carregue esquemas para a sua conta de integração.

### <a name="validation"></a>Validação
Depois dos dados de entrada são convertidos para XML (ou se o XML foi o formato da mensagem recebido), a validação é executado para determinar se a mensagem respeita o esquema XSD. Para fazê-lo em Logic Apps, utilize o [validação XML](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) ação. Novamente, pode utilizar os mesmos esquemas do BizTalk Services sem quaisquer alterações.

### <a name="transform-messages"></a>Mensagens de transformação
Nos serviços do BizTalk, a fase de transformação converte um formato de mensagem baseado em XML para outro. Isto é feito aplicando um mapa, utilizando o mapeador de TRFM. As Logic Apps, o processo é semelhante. A ação de transformação executa um mapa da sua conta de integração. A principal diferença é que o maps em Logic Apps estão no formato XSLT. XSLT inclui a capacidade de reutilizar XSLT existente já tiver, incluindo o maps criados para BizTalk Server, que contêm functoids. 

### <a name="routing-rules"></a>Regras de encaminhamento
BizTalk Services faz uma decisão de encaminhamento no qual ponto final do conector para enviar mensagens/dados de entrada. A capacidade de selecionar um dos vários pontos finais previamente configurados é possível utilizar a opção de filtro de encaminhamento:

![](media/logic-apps-move-from-mabs/route-filter.png)

As Logic Apps fornece capacidades de lógica mais sofisticadas com [condição](../logic-apps/logic-apps-use-logic-app-features.md) e [comutador](../logic-apps/logic-apps-switch-case.md), ativar o fluxo de controlo avançada e o encaminhamento. Converter os filtros de encaminhamento nos BizTalk Services melhor alcançada através de um **condição** *se* existem apenas duas opções. Se existir mais do que dois, em seguida, utilize um **comutador**.

### <a name="enrich"></a>Enriqueça
A fase de Enrich no processamento do BizTalk Services Adiciona propriedades para o contexto de mensagem associado com os dados recebidos. Por exemplo, ao promover uma propriedade a utilizar para o encaminhamento (abordados abaixo) a partir de uma pesquisa de base de dados, ou por extrair um valor utilizando uma expressão de XPath. As Logic Apps fornece acesso a todas as saídas de dados contextuais de precedente ações, tornando-o simples replicar o mesmo comportamento. Por exemplo, utilizando o `Get Row` ação de ligação do SQL Server, devolver dados a partir de uma base de dados do SQL Server e utilizar os dados numa ação decisões de encaminhamento. Da mesma forma, propriedades no barramento de serviço receber mensagens em fila por um acionador são endereçável, bem como XPath utilizando a expressão de linguagem de definição de fluxo de trabalho xpath.

### <a name="use-custom-code"></a>Utilizar código personalizado
Os BizTalk Services fornecem a capacidade de [executar código personalizado](https://msdn.microsoft.com/library/azure/dn232389.aspx) carregado na suas própria assemblagens. Isto é implementado pelo [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector.aspx) interface. Cada fase a bridge inclui duas propriedades (no Inspector introduza e no Inspector de saída) que fornecem o tipo de .net que criou que implemente esta interface. Código personalizado permite-lhe efetuar o processamento de mais complexo nos dados, bem como a reutilizar o código existente em assemblagens que executam a lógica de negócio comuns. 

As Logic Apps fornece duas formas de principais para executar código personalizado: das funções do Azure e API Apps. As funções do Azure podem ser criadas e chamadas a partir das logic apps. Consulte [adicionar e executar código personalizado para aplicações lógicas através das funções do Azure](../logic-apps/logic-apps-azure-functions.md). Utilize a API Apps, parte do App Service do Azure, para criar as suas próprias acionadores e ações. Saiba mais sobre [criar uma API personalizada a utilizar com as Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Se tiver o código personalizado no assmeblies que chamar a partir dos BizTalk Services, pode mover este código para as funções do Azure, ou criar APIs personalizadas com aplicações API. consoante o que estiver a implementar. Por exemplo, se tiver o código que encapsula num wrapper a outro serviço que Logic Apps não tem um conector, em seguida, criar uma aplicação API e utilizar as ações que a aplicação API fornece na sua aplicação lógica. Se tiver de funções de programa auxiliar ou bibliotecas, em seguida, das funções do Azure é, provavelmente, a melhor opção.

### <a name="edi-processing-and-trading-partner-management"></a>EDI processar e gestão de parceiros comerciais
BizTalk Services inclui processamento EDI e B2B com suporte para AS2 (declaração de aplicabilidade 2), X12 e EDIFACT. Por isso, não as Logic Apps. Nos serviços do BizTalk, a criar bridges EDI e criar/Gerir parceiros comerciais e de contratos no portal de gestão e o controlo dedicado.

Em Logic Apps, esta funcionalidade está incluída com o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). Isto é composto pelas conta de integração e B2B as ações de processamento EDI e B2B. O [Integration Account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) é utilizado para criar e gerir [comerciais parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md) e [contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md). Depois de criar uma conta de integração, pode associar um ou mais as logic apps para a conta. Depois de associados, pode utilizar as ações de B2B para aceder às informações do parceiro comercial na sua aplicação lógica. São fornecidas as seguintes ações:

* Codificar AS2
* Descodificar AS2
* Codificar X12
* Descodificar o X12
* EDIFACT codificar
* EDIFACT descodificar

Ao contrário dos BizTalk Services, estas ações são dissociadas dos protocolos de transporte. Por isso, quando criar as logic apps, tem mais flexibilidade no quais conectores que utilize para enviar e receber dados. Por exemplo, de possíveis para receber X12 ficheiros como anexos de e-mail e o processo, em seguida, estes ficheiros de uma aplicação lógica. 

## <a name="manage-and-monitor"></a>Gerir e monitorizar
Bem como gestão de parceiros comerciais, o portal dedicado dos BizTalk Services fornecidas capacidades de controlo para monitorizar e resolver problemas. 

As Logic Apps fornece capacidades de monitorização e controlo mais rico de [portal do Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md)e com o [Operations Management Suite B2B solução](../logic-apps/logic-apps-monitor-b2b-message.md), incluindo uma aplicação móvel para manter a par no coisas quando estiver em viagem.

## <a name="high-availability"></a>Elevada disponibilidade
Para alcançar a elevada disponibilidade (HA) nos BizTalk Services, pode utilizar mais do que uma instância uma determinada região para partilhar a carga de processamento. Com as logic apps, na região HA está incorporada e ficar sem custos adicionais. Fora da região recuperação de desastres para processamento de B2B nos BizTalk Services, é necessário um processo de cópia de segurança e restauro. Em Logic Apps, um ativo/passivo por várias regiões [capacidade de DR](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md) é fornecido; que permite a sincronização de dados de B2B em contas de automatização em diferentes regiões de continuidade do negócio.

## <a name="next"></a>Seguinte
* [O que são as aplicações lógicas](logic-apps-what-are-logic-apps.md)
* [Criar a sua primeira aplicação lógica](logic-apps-create-a-logic-app.md) ou começar rapidamente com um [modelo compilado previamente](logic-apps-create-logic-apps-from-templates.md)  
* [Ver todos os conectores disponíveis](../connectors/apis-list.md) pode usar numa aplicação lógica
