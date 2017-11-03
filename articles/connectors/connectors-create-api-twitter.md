---
title: Saiba como utilizar o conector do Twitter nas logic apps | Microsoft Docs
description: "Descrição geral do conector do Twitter com parâmetros de REST API"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: be8163043535833ce45b3d50939a537406cf8152
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-twitter-connector"></a>Começar a utilizar o conector do Twitter
Com o conector do Twitter, pode:

* Publica tweets e get tweets
* Linhas cronológicas de acesso, amigos e followers
* Efetue qualquer uma das outras ações e acionadores descritos abaixo  

Para utilizar [qualquer conector](apis-list.md), terá primeiro de criar uma aplicação lógica. Pode começar a utilizar pelo [criar uma aplicação lógica agora](../logic-apps/logic-apps-create-a-logic-app.md).  

## <a name="connect-to-twitter"></a>Ligar ao Twitter
Antes da aplicação lógica pode aceder a qualquer serviço, terá primeiro de criar um *ligação* ao serviço. A [ligação](connectors-overview.md) fornece conectividade entre uma aplicação lógica e outro serviço.  

### <a name="create-a-connection-to-twitter"></a>Criar uma ligação ao Twitter
> [!INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-twitter.md)]
> 
> 

## <a name="use-a-twitter-trigger"></a>Utilizar um acionador do Twitter
Um acionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho definido numa aplicação lógica. [Saiba mais sobre acionadores](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

Neste exemplo, posso irá mostrar-lhe como utilizar o **quando é registado um tweet novo** acionador para procurar #Seattle e, se for encontrado #Seattle, atualizar um ficheiro no Dropbox com o texto do tweet. Um exemplo de enterprise, foi possível procurar o nome da sua empresa e atualizar uma base de dados do SQL Server com o texto do tweet.

1. Introduza *twitter* na caixa de pesquisa no designer de aplicações lógicas, em seguida, selecione o **Twitter - quando um tweet nova é publicado** acionador   
   ![Imagem de Acionador de twitter 1](./media/connectors-create-api-twitter/trigger-1.png)  
2. Introduza *#Seattle* no **texto de pesquisa** controlo  
   ![Imagem de Acionador de twitter 2](./media/connectors-create-api-twitter/trigger-2.png) 

Neste momento, a aplicação lógica foi configurada com um acionador que irá iniciar a execução dos outros acionadores e ações no fluxo de trabalho. 

> [!NOTE]
> Para uma aplicação lógica que seja funcional, tem de conter pelo menos um acionador e uma ação. Siga os passos na secção seguinte para adicionar uma ação.  
> 
> 

## <a name="add-a-condition"></a>Adicionar uma condição
Uma vez que vamos só esteja interessados em tweets dos utilizadores com mais de 50 utilizadores, uma condição que confirma o número de followers tem de ser adicionada para a aplicação lógica.  

1. Selecione **+ novo passo** para adicionar a ação que gostaria de tomar quando #Seattle é encontrado num tweet novo  
   ![Imagem da ação twitter 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
2. Selecione o **adicionar uma condição** ligação.  
   ![Imagem de condição do twitter 1](../../includes/media/connectors-create-api-twitter/condition-1.png)   
   Esta ação abre o **condição** onde pode verificar, tais como condições de controlo *é igual ao*, *é inferior a*, *é superior ao*, *contém*, etc.  
   ![Imagem de condição do twitter 2](../../includes/media/connectors-create-api-twitter/condition-2.png)   
3. Selecione o **escolha um valor** controlo.  
   Neste controlo, pode selecionar um ou mais das propriedades de quaisquer ações anteriores ou acionadores como o valor cuja condição será avaliada como true ou false.
   ![Imagem de condição do twitter 3](../../includes/media/connectors-create-api-twitter/condition-3.png)   
4. Selecione o **...**  para expandir a lista de propriedades, para que possa ver todas as propriedades que estão disponíveis.        
   ![Imagem de condição do twitter 4](../../includes/media/connectors-create-api-twitter/condition-4.png)   
5. Selecione o **contagem Followers** propriedade.    
   ![Imagem de condição do twitter 5](../../includes/media/connectors-create-api-twitter/condition-5.png)   
6. Tenha em atenção de que a propriedade de contagem de Followers está agora no controlo de valor.    
   ![Imagem de condição do twitter 6](../../includes/media/connectors-create-api-twitter/condition-6.png)   
7. Selecione **é superior ao** na lista de operadores.    
   ![Imagem de condição do twitter 7](../../includes/media/connectors-create-api-twitter/condition-7.png)   
8. Introduza 50 como o operando para o *é superior ao* operador.  
   A condição é agora adicionada. Guardar o seu trabalho, utilizando o **guardar** ligação no menu acima.    
   ![Imagem de condição do twitter 8](../../includes/media/connectors-create-api-twitter/condition-8.png)   

## <a name="use-a-twitter-action"></a>Utilizar uma ação do Twitter
Uma ação é uma operação levada a cabo pelo fluxo de trabalho definido numa aplicação lógica. [Saiba mais sobre as ações](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

Agora que adicionou um acionador, siga estes passos para adicionar uma ação que irá publicar um tweet novo com os conteúdos de tweets encontrados pelo acionador. Para efeitos desta passagem apenas tweets dos utilizadores com mais de 50 followers vai ser publicadas.  

No próximo passo, irá adicionar uma ação de Twitter que irá publicar um tweet utilizar algumas das propriedades de cada tweet que publicou por um utilizador que tem mais de 50 followers.  

1. Selecione **adicionar uma ação**. Esta ação abre o controlo de procura, onde pode procurar outras ações e é acionado.  
   ![Imagem de condição do twitter 9](../../includes/media/connectors-create-api-twitter/condition-9.png)   
2. Introduza *twitter* na caixa de pesquisa, em seguida, selecione o **Twitter - publique um tweet** ação. Esta ação abre o **publique um tweet** controlar onde será introduza todos os detalhes para tweet que está a ser publicado.      
   ![Imagem de ação 1 a 5 do twitter](../../includes/media/connectors-create-api-twitter/action-1-5.png)   
3. Selecione o **Tweet texto** controlo. Todas as saídas de ações anteriores e acionadores na aplicação lógica agora estão visíveis. Pode selecionar qualquer uma destas e utilizá-los como parte do texto tweet de tweet de novo.     
   ![Imagem da ação twitter 2](../../includes/media/connectors-create-api-twitter/action-2.png)   
4. Selecione **nome de utilizador**   
5. Introduza *diz:* no controlo de texto tweet. Fazê-lo imediatamente após o nome de utilizador.  
6. Selecione *Tweet texto*.       
   ![Imagem da ação twitter 3](../../includes/media/connectors-create-api-twitter/action-3.png)   
7. Guarde o trabalho e enviar um tweet com o hashtag #Seattle para ativar o fluxo de trabalho.  


## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/twitterconnector/). 

## <a name="next-steps"></a>Passos seguintes
[Criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md)

