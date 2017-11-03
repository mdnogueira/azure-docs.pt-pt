---
title: Utilizar o conector de Slack nas suas Azure logic apps | Microsoft Docs
description: Ligar-se ao Slack nas suas logic apps
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: fc5fc128efe01bd0727e3ff30d8938918e89ac3a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-slack-connector"></a>Começar a utilizar o conector Slack
Slack é uma ferramenta de comunicação de equipa, que reúne todas as suas comunicações equipa num colocar, de forma instantânea pesquisáveis e disponível onde quer que esteja. 

Começar através da criação de uma aplicação lógica agora; consulte [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-slack"></a>Criar uma ligação ao Slack
Para utilizar o conector Slack, tem primeiro de criar um **ligação** , em seguida, forneça os detalhes para estas propriedades: 

| Propriedade | Necessário | Descrição |
| --- | --- | --- |
| Token |Sim |Forneça credenciais Slack |

Siga estes passos para iniciar sessão no Slack e concluir a configuração de Slack **ligação** na sua aplicação lógica:

1. Selecione **periodicidade**
2. Selecione um **frequência** e introduza um **intervalo**
3. Selecione **adicionar uma ação**  
   ![Configurar Slack][1]  
4. Introduza Slack na caixa de pesquisa e aguarde que a procura para devolver todas as entradas com Slack no nome
5. Selecione **Slack - mensagem Post**
6. Selecione **iniciar sessão para Slack**:  
   ![Configurar Slack][2]
7. Indique as suas credenciais Slack para iniciar sessão para autorizar a aplicação    
   ![Configurar Slack][3]  
8. Será redirecionado para a página de início de sessão da sua organização. **Autorizar** Slack para interagir com a sua aplicação lógica:      
   ![Configurar Slack][5] 
9. Depois de concluída a autorização irá ser redirecionados para a sua aplicação lógica para concluí-la ao configurar o **Slack - obter todas as mensagens** secção. Adicione outros acionadores e ações que precisa.  
   ![Configurar Slack][6]
10. Guarde o trabalho selecionando **guardar** na barra de menus acima.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/slack/).

## <a name="more-connectors"></a>Mais conectores
Volte à [lista APIs](apis-list.md).

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
