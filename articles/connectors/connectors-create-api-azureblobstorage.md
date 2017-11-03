---
title: Adicione o conector do armazenamento de Blobs do Azure nas suas Logic Apps | Microsoft Docs
description: "Começar a utilizar e configurar o conector de armazenamento de Blobs do Azure numa aplicação lógica"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5dc3f75-6bea-420b-b250-183668d2848d
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/02/2017
ms.author: mandia; ladocs
ms.openlocfilehash: bc7908868828bd1628633cf9e57f8c44f8000827
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-blob-storage-connector-in-a-logic-app"></a>Utilizar o conector de armazenamento de Blobs do Azure numa aplicação lógica
Utilize o conector de armazenamento de Blobs do Azure para carregar, atualizar, obter e eliminar os blobs na sua conta de armazenamento, tudo dentro de uma aplicação lógica.  

Com o blob storage do Azure, pode:

* Crie o fluxo de trabalho ao carregar novos projetos ou obtenção de ficheiros que foi actualizados recentemente.
* Utilize ações para obter os metadados de ficheiro, eliminar um ficheiro, copiar ficheiros e muito mais. Por exemplo, quando uma ferramenta é atualizada num web site do Azure (um acionador), em seguida, atualize um ficheiro no armazenamento de BLOBs (uma ação). 

Este tópico mostra como utilizar o conector de armazenamento de BLOBs numa aplicação lógica.

Para saber mais sobre Logic Apps, consulte o artigo [que são logic apps](../logic-apps/logic-apps-what-are-logic-apps.md) e [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

Para saber mais sobre Logic Apps, consulte o artigo [que são logic apps](../logic-apps/logic-apps-what-are-logic-apps.md) e [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-azure-blob-storage"></a>Ligar ao armazenamento de Blobs do Azure
Antes da aplicação lógica pode aceder a qualquer serviço, tem primeiro de criar um *ligação* ao serviço. Uma ligação oferece a conectividade entre uma aplicação lógica e outro serviço. Por exemplo, para ligar a uma conta do storage, tem primeiro de criar um armazenamento de BLOBs *ligação*. Para criar uma ligação, introduza as credenciais que normalmente utiliza para aceder ao serviço que está a ligar. Por isso, com o storage do Azure, introduza as credenciais para a sua conta de armazenamento para criar a ligação. 

#### <a name="create-the-connection"></a>Criar a ligação
> [!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="use-a-trigger"></a>Utilizar um acionador
Este conector não ter acionadores. Utilize outros acionadores para iniciar a aplicação lógica, tais como um acionador de recorrência, um acionador de HTTP Webhook, acionadores disponíveis com outros conectores e muito mais. [Criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md) fornece um exemplo.

## <a name="use-an-action"></a>Utilizar uma ação
Uma ação é uma operação levada a cabo pelo fluxo de trabalho definido numa aplicação lógica.

1. Selecione o sinal de adição. Pode ver várias opções: **adicionar uma ação**, **adicionar uma condição**, ou um do **mais** opções.
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. Escolha **adicionar uma ação**.
3. Na caixa de texto, escreva "blob" para obter uma lista de todas as ações disponíveis.
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png) 
4. No nosso exemplo, escolha **AzureBlob - utilizar caminho de metadados do ficheiro de Get**. Se já existe uma ligação, em seguida, selecione o **...** Botão (selecionador de mostrar) para selecionar um ficheiro.
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    Se lhe for pedido para obter as informações de ligação, em seguida, introduza os detalhes para criar a ligação. [Criar a ligação](connectors-create-api-azureblobstorage.md#create-the-connection) neste tópico descreve estas propriedades. 
   
   > [!NOTE]
   > Neste exemplo, vamos obter os metadados de um ficheiro. Para ver os metadados, adicione outra ação que cria um novo ficheiro utilizando o conector do outro. Por exemplo, adicione uma ação de OneDrive que cria um novo ficheiro de "teste" com base nos metadados. 


5. **Guardar** as suas alterações (canto superior esquerdo da barra de ferramentas). A aplicação lógica é guardada e pode ser ativada automaticamente.

> [!TIP]
> [Explorador de armazenamento](http://storageexplorer.com/) é uma ótima ferramenta para gerir várias contas de armazenamento.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver todos os acionadores e ações definidas no swagger e consulte também os limites no [detalhes do conector](/connectors/azureblobconnector/). 

## <a name="next-steps"></a>Passos seguintes
[Criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md). Explorar os outros conectores disponíveis em Logic Apps no nosso [lista APIs](apis-list.md).

