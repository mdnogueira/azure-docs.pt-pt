---
title: Utilizar o conector do servidor do SharePoint nas suas Logic Apps | Microsoft Docs
description: "Começar a utilizar o conector do SharePoint Server, nas suas Logic apps"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 0f3274816e279a1aa57febaa2f8294914900799a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-sharepoint-connector"></a>Começar a utilizar o conector do SharePoint
O conector do SharePoint fornece uma forma de trabalhar com apresenta uma lista do SharePoint.

Começar através da criação de uma aplicação lógica; consulte [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-sharepoint"></a>Criar uma ligação para o SharePoint
Para utilizar o conector do SharePoint, tem primeiro de criar um **ligação** , em seguida, forneça os detalhes para estas propriedades: 

| Propriedade | Necessário | Descrição |
| --- | --- | --- |
| Token |Sim |Forneça credenciais do SharePoint |

Para ligar ao **SharePoint**, introduza a sua identidade (nome de utilizador e palavra-passe, smart card credenciais, etc.) para SharePoint. Uma vez que já foi autenticado, pode continuar a utilizar o conector do SharePoint na sua aplicação lógica. 

Enquanto no designer da sua aplicação lógica, siga estes passos para iniciar sessão no SharePoint para criar a ligação **ligação** para utilização na sua aplicação lógica:

1. Introduza o SharePoint na caixa de pesquisa e aguarde que a procura para devolver todas as entradas com o SharePoint no nome:   
   ![Configurar o SharePoint][1]  
2. Selecione **SharePoint - quando é criado um ficheiro**   
3. Selecione **inicie sessão no SharePoint**:   
   ![Configurar o SharePoint][2]    
4. Fornecer as credenciais do SharePoint para iniciar sessão autenticar com o SharePoint   
   ![Configurar o SharePoint][3]     
5. Depois de concluir a autenticação irá ser redirecionado para a sua aplicação lógica para concluí-la através da configuração do SharePoint **quando é criado um ficheiro** caixa de diálogo.          
   ![Configurar o SharePoint][4]  
6. Em seguida, pode adicionar outros acionadores e ações que precisa para concluir a sua aplicação lógica.   
7. Guarde o trabalho selecionando **guardar** na barra de menus acima.  

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/sharepoint/).

## <a name="more-connectors"></a>Mais conectores
Volte à [lista APIs](apis-list.md).

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
