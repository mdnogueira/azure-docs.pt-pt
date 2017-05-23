---
title: "Criar uma função no Azure acionada pelo Armazenamento de blobs | Microsoft Docs"
description: "Utilize as Funções do Azure para criar uma função sem servidores que é invocada por itens que são adicionados ao Armazenamento de blobs do Azure."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: c0d1271bc083688bbc72bd2556546c2f738e7345
ms.contentlocale: pt-pt
ms.lasthandoff: 05/12/2017


---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Criar uma função acionada pelo Armazenamento de blobs do Azure

Saiba como criar uma função que é acionada quando são carregados ou atualizados ficheiros no Armazenamento de blobs do Azure.

![Ver mensagem nos registos.](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar este exemplo, tem de ter feito o seguinte:

- Transferir e instalar o [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Criar uma aplicação de Funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Aplicação Function App criada com êxito.](./media/functions-create-first-azure-function/function-app-create-success.png)

Em seguida, vai criar uma função na aplicação Function App nova.

<a name="create-function"></a>

## <a name="create-a-blob-storage-triggered-function"></a>Criar uma função acionada pelo Armazenamento de blobs

Expanda a aplicação Function App, clique no botão **+** junto a **Functions** e clique no modelo **BlobTrigger** da sua linguagem preferida. Em seguida, utilize as definições especificadas na tabela e clique em **Criar**.

![Crie a função acionada pelo Armazenamento de blobs.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)

| Definição | Valor sugerido | Descrição |
|---|---|---|
| **Caminho**   | mycontainer/{name}    | Localização no Armazenamento de blobs a ser monitorizado. O nome do ficheiro do blob é transmitido no enlace como o parâmetro _name_.  |
| **Ligação da conta de armazenamento** | AzureWebJobStorage | Pode utilizar a ligação da conta de armazenamento que já está a ser utilizada pela sua aplicação Function App ou criar uma nova.  |
| **Dar um nome à função** | Exclusivo na aplicação Function App | O nome desta função acionada por fila. |

Em seguida, vai ligar à sua conta de Armazenamento do Azure e criar o contentor **mycontainer**.

## <a name="create-the-container"></a>Criar o contentor

1. Na sua função, clique em **Integrar**, expanda **Documentação** e copie **Nome da conta** e **Chave da conta**. Vai utilizar estas credenciais para ligar à conta de armazenamento. Se já tiver ligado a sua conta de armazenamento, avance para o passo 4.

    ![Obtenha as credenciais de ligação da conta de Armazenamento.](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

1. Execute a ferramenta [Microsoft Azure Storage Explorer](http://storageexplorer.com/), clique no ícone de ligação à esquerda, escolha **Utilizar um nome e uma chave de conta de armazenamento** e clique em **Seguinte**.

    ![Execute a ferramenta Microsoft Azure Storage Explorer.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)

1. Introduza o **Nome da conta** e a **Chave da conta** do passo 1, clique em **Seguinte** e em **Ligar**. 

    ![Introduza as credenciais de armazenamento e ligue-se.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

1. Expanda a conta de armazenamento anexada, clique com o botão direito do rato em **Contentores de blobs**, clique em **Criar contentor de blobs**, escreva `mycontainer` e prima Enter.

    ![Crie uma fila de armazenamento.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

Agora que já tem um contentor de blobs, pode carregar um ficheiro para o mesmo para testar a função.

## <a name="test-the-function"></a>Testar a função

1. Novamente no portal do Azure, navegue para a função, expanda os **Registos**, na parte inferior da página, e confirme que a transmissão de registos não está em pausa.

1. No Storage Explorer, expanda a conta de armazenamento, **Contentores de blobs** e **mycontainer**. Clique em **Carregar** e em **Carregar ficheiros...**.

    ![Carregue um ficheiro para o contentor de blobs.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

1. Na caixa de diálogo **Carregar ficheiros**, clique no campo **Ficheiros**. Procure um ficheiro no computador local, como um ficheiro de imagem, selecione-o, clique em **Abrir** e em **Carregar**.

1. Volte para os registos da função e confirme que o blob foi lido.

   ![Ver mensagem nos registos.](./media/functions-create-storage-blob-triggered-function/functions-blob-storage-trigger-view-logs.png)

    >[!NOTE]
    > Quando a aplicação Function App é executada no plano Consumo predefinido, poderá haver um atraso de vários minutos entre a inclusão ou atualização do blob e o acionamento da função. Se precisar de baixa latência nas suas funções acionadas por blobs, considere executar a sua aplicação Function App num plano do Serviço de Aplicações.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Criou uma função que é executada quando um blob é adicionado ou atualizado no Armazenamento de blobs. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações sobre os acionadores do Armazenamento de blobs, veja [Azure Functions Blob storage bindings](functions-bindings-storage-blob.md) (Enlaces do Armazenamento de blobs das Funções do Azure).
