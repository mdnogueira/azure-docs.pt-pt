---
title: Adicione o conector do SQL Database do Azure nas suas Logic Apps | Microsoft Docs
description: "Descrição geral do conector do SQL Database do Azure com os parâmetros de REST API"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: a3d5cb909dbfcb00f3fbfa0165bb6cd58eb18688
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-azure-sql-database-connector"></a>Começar a utilizar o conector do SQL Database do Azure
Utilizar o conector do SQL Database do Azure, crie fluxos de trabalho para a sua organização que gerem dados no seu tabelas. 

Base de dados SQL, pode:

* Crie o fluxo de trabalho ao adicionar um novo cliente para uma base de dados de clientes ou atualizar uma ordem numa base de dados ordens.
* Utilize ações para obter uma linha de dados, insira uma nova linha e até mesmo eliminar. Por exemplo, quando é criado um registo no Dynamics CRM Online (um acionador), insira uma linha numa SQL Database do Azure (uma ação). 

Este tópico mostra-lhe como utilizar o conector de base de dados SQL numa aplicação lógica e também lista as ações.

Para saber mais sobre Logic Apps, consulte o artigo [que são logic apps](../logic-apps/logic-apps-what-are-logic-apps.md) e [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-azure-sql-database"></a>Ligar à base de dados SQL do Azure
Antes da aplicação lógica pode aceder a qualquer serviço, tem primeiro de criar um *ligação* ao serviço. Uma ligação oferece a conectividade entre uma aplicação lógica e outro serviço. Por exemplo, para ligar à base de dados do SQL Server, tem primeiro de criar uma base de dados do SQL Server *ligação*. Para criar uma ligação, introduza as credenciais que normalmente utiliza para aceder ao serviço que está a ligar. Por isso, na base de dados do SQL Server, introduza as credenciais da base de dados SQL para criar a ligação. 

#### <a name="create-the-connection"></a>Criar a ligação
> [!INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]
> 
> 

## <a name="use-a-trigger"></a>Utilizar um acionador
Este conector não ter acionadores. Utilize outros acionadores para iniciar a aplicação lógica, tais como um acionador de recorrência, um acionador de HTTP Webhook, acionadores disponíveis com outros conectores e muito mais. [Criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md) fornece um exemplo.

## <a name="use-an-action"></a>Utilizar uma ação
Uma ação é uma operação levada a cabo pelo fluxo de trabalho definido numa aplicação lógica. [Saiba mais sobre as ações](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

1. Selecione o sinal de adição. Pode ver várias opções: **adicionar uma ação**, **adicionar uma condição**, ou um do **mais** opções.
   
    ![](./media/connectors-create-api-sqlazure/add-action.png)
2. Escolha **adicionar uma ação**.
3. Na caixa de texto, escreva "sql" para obter uma lista de todas as ações disponíveis.
   
    ![](./media/connectors-create-api-sqlazure/sql-1.png) 
4. No nosso exemplo, escolha **do SQL Server - Get linha**. Se já existe uma ligação, em seguida, selecione o **nome da tabela** da lista pendente lista e introduza o **ID de linha** pretender regressar.
   
    ![](./media/connectors-create-api-sqlazure/sample-table.png)
   
    Se lhe for pedido para obter as informações de ligação, em seguida, introduza os detalhes para criar a ligação. [Criar a ligação](connectors-create-api-sqlazure.md#create-the-connection) neste tópico descreve estas propriedades. 
   
   > [!NOTE]
   > Neste exemplo, iremos devolver uma linha de uma tabela. Para ver os dados nesta linha, adicione outra ação que cria um ficheiro com os campos da tabela. Por exemplo, adicione uma ação de OneDrive que utiliza os campos FirstName e LastName para criar um novo ficheiro na conta de armazenamento na nuvem. 
   > 
   > 
5. **Guardar** as suas alterações (canto superior esquerdo da barra de ferramentas). A aplicação lógica é guardada e pode ser ativada automaticamente.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/sql/). 

## <a name="next-steps"></a>Passos seguintes
[Criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md). Explorar os outros conectores disponíveis em Logic Apps no nosso [lista APIs](apis-list.md).

