---
title: Utilize grelha de eventos do Azure para automatizar o redimensionamento carregado imagens | Microsoft Docs
description: "Grelha de eventos do Azure pode acionar num carregamentos de blob no Storage do Azure. Pode utilizar este para enviar os ficheiros de imagem carregados para o armazenamento do Azure para outros serviços, como as funções do Azure, para redimensionar e outras melhorias."
services: event-grid
author: ggailey777
manager: cfowler
editor: 
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 709d23ab590c06d5da9b03e2767bc0be5905355b
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="automate-resizing-uploaded-images-using-event-grid"></a>Automatizar o redimensionamento de imagens carregadas com a grelha de eventos

[Grelha de eventos do Azure](overview.md) é um serviço de eventos CCM para a nuvem. Grelha de eventos permite-lhe criar subscrições de eventos gerados pelos serviços do Azure ou recursos de terceiros.  

Este tutorial faz parte dois uma série de tutoriais de armazenamento. Se expande o [tutorial de armazenamento anterior] [ previous-tutorial] adicionar sem servidor em miniatura a geração automática a grelha de eventos do Azure e as funções do Azure a utilizar. Permite a grelha de evento [das funções do Azure](..\azure-functions\functions-overview.md) para responder a [Blob storage do Azure](..\storage\blobs\storage-blobs-introduction.md) eventos e gerar miniaturas de imagens carregadas. Uma subscrição de eventos é criada contra o Blob storage criar eventos. Quando é adicionado um blob para um contentor de armazenamento de Blob específico, um ponto final da função é chamado. Os dados transmitidos para o enlace de função da grelha de eventos são utilizados para aceder a BLOBs e gerar na imagem em miniatura. 

Utilizar a CLI do Azure e o portal do Azure para adicionar a funcionalidade de redimensionamento para uma aplicação de carregamento de imagem existente.

![Aplicação web publicada no Edge browser](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento do Azure geral
> * Implementar código sem servidor utilizando as funções do Azure
> * Criar uma subscrição de evento de armazenamento de BLOBs na grelha de eventos

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

+ Tem de ter concluído o tutorial de armazenamento de BLOBs anterior: [carregar dados de imagem na nuvem com o Storage do Azure][previous-tutorial]. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tópico requer que está a executar a CLI do Azure versão 2.0.14 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Se não estiver a utilizar o Cloud Shell, tem primeiro de iniciar sessão com o `az login`.

## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

As funções do Azure requer uma conta de armazenamento geral. Criar uma conta de armazenamento geral separada no grupo de recursos utilizando o [criar conta de armazenamento az](/cli/azure/storage/account#create) comando.

Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. 

O seguinte comando, substitua o seus próprios nome globalmente exclusivo para a conta de armazenamento geral onde vir o `<general_storage_account>` marcador de posição. 

```azurecli-interactive
az storage account create --name <general_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind storage
```

## <a name="create-a-function-app"></a>Criar uma aplicação de função  

Tem de ter uma aplicação de função para alojar a execução da sua função. A aplicação Function App proporciona um ambiente para a execução sem servidor do código da sua função. Utilize o comando [az functionapp create](/cli/azure/functionapp#create) para criar uma aplicação Function App. 

O seguinte comando, substitua o seu próprio nome de aplicação de função exclusivo onde vir o `<function_app>` marcador de posição. O `<function_app>` vai ser utilizado como o domínio DNS predefinido para a aplicação Function App, daí que o nome tenha de ser exclusivo em todas as aplicações no Azure. Neste caso, `<general_storage_account>` é o nome da conta de armazenamento geral que criou.  

```azurecli-interactive
az functionapp create --name <function_app> --storage-account  <general_storage_account>  \
--resource-group myResourceGroup --consumption-plan-location westcentralus
```

Agora tem de configurar a aplicação de função para ligar ao armazenamento de Blobs. 

## <a name="configure-the-function-app"></a>Configurar a aplicação de função

A função tem da cadeia de ligação para ligar à conta de armazenamento de Blobs. Neste caso, `<blob_storage_account>` é o nome da conta de armazenamento de BLOBs que criou no tutorial anterior. Obter a cadeia de ligação com o [cadeia de ligação de mostrar de conta de armazenamento az](/cli/azure/storage/account#show-connection-string) comando. O nome do contentor de imagem em miniatura também tem de ser definido `thumbs`. Adicionar estas definições de aplicação na aplicação de função com o [az functionapp configuração appsettings conjunto](/cli/azure/functionapp/config/appsettings#set) comando.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <blob_storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings myblobstorage_STORAGE=$storageConnectionString \
myContainerName=thumbs
```

Agora pode implementar um projeto de código de função para esta aplicação de função.

## <a name="deploy-the-function-code"></a>Implementar o código de função 

A função de c# que executa o redimensionamento de imagens está disponível neste [repositório do GitHub exemplo](https://github.com/Azure-Samples/function-image-upload-resize). Implementar este projeto de código de funções para a aplicação de função utilizando o [az functionapp configuração da origem de implementação](/cli/azure/functionapp/deployment/source#config) comando. 

O comando seguinte, `<function_app>` é a mesma aplicação de função que criou no script anterior.

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

A função de redimensionamento da imagem é acionada por uma subscrição de eventos para um evento de Blob criado. Os dados transmitidos para o acionador incluem o URL do blob, que por sua vez é transmitido para o enlace de entrada para obter a imagem carregada a partir do Blob storage. A função gera uma imagem em miniatura e escreve no fluxo resultante para um contentor separado no Blob storage. Para obter mais informações sobre esta função, consulte o [ficheiro Leia-me no repositório de exemplo](https://github.com/Azure-Samples/function-image-upload-resize/blob/master/README.md).
 
O código de projeto de função é implementado diretamente a partir do repositório de exemplo público. Para saber mais sobre as opções de implementação para as funções do Azure, consulte [a implementação contínua para as funções do Azure](../azure-functions/functions-continuous-deployment.md).

## <a name="create-your-event-subscription"></a>Criar a sua subscrição de evento

Uma subscrição de evento indica os eventos gerados pelo fornecedor pretende enviadas para um ponto final específico. Neste caso, o ponto final é exposto pela sua função. Utilize os seguintes passos para criar uma subscrição de evento da sua função no portal do Azure: 

1. No [portal do Azure](https://portal.azure.com), clique na seta na parte inferior esquerda para expandir a todos os serviços, escreva `functions` no **filtro** campo e, em seguida, escolha **aplicações de função**. 

    ![Navegue para aplicações de função no portal do Azure](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. Expanda a sua aplicação de função, escolha o **imageresizerfunc** funcionar e, em seguida, selecione **subscrição de adicionar o evento grelha**.

    ![Navegue para aplicações de função no portal do Azure](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. Utilize as definições de subscrição de evento especificado na tabela.

    ![Criar subscrição de evento na função no portal do Azure](./media/resize-images-on-storage-blob-upload-event/event-subscription-create-flow.png)

    | Definição      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome** | imageresizersub | Nome que identifica a sua nova subscrição de evento. | 
    | **Tipo de tópico** |  Contas de armazenamento | Escolha o fornecedor de eventos de conta de armazenamento. | 
    | **Subscrição** | A sua subscrição | Por predefinição, a sua subscrição atual deve ser selecionada.   |
    | **Grupo de recursos** | myResourceGroup | Selecione **utilizar existente** e escolha o grupo de recursos que estiver a utilizar neste tópico.  |
    | **Instância** |  `<blob_storage_account>` |  Selecione a conta de armazenamento de BLOBs que criou. |
    | **Tipos de evento** | Blob criado | Desmarcar todos os tipos diferentes de **Blob criado**. Apenas os tipos de evento de `Microsoft.Storage.BlobCreated` são transmitidos para a função.| 
    | **Ponto final de subscritor** | gerado automaticamente | Utilize o URL de ponto final que é gerado por si. | 
    | **Filtro de prefixo** | / blobServices/predefinido/contentores/imagens/blobs / | Filtra eventos de armazenamento apenas no **imagens** contentor.| 

4. Clique em **criar** para adicionar a subscrição de evento. Esta ação cria uma subscrição de evento que aciona **imageresizerfunc** quando um blob é adicionado ao **imagens** contentor. São adicionadas imagens dimensionadas para o **thumbs** contentor.

Agora que os serviços de back-end estiverem configurados, testar a funcionalidade de redimensionamento da imagem na aplicação de web de exemplo. 

## <a name="test-the-sample-app"></a>Testar a aplicação de exemplo

Para testar a imagem de redimensionamento na aplicação web, navegue para o URL da sua aplicação publicada. O URL predefinido da aplicação Web é `https://<web_app>.azurewebsites.net`.

Clique em de **carregar fotografias** região para selecionar e carregar um ficheiro. Também pode arrastar uma fotografia para esta região. 

Tenha em atenção que, depois da imagem carregada desaparece, uma cópia da imagem carregada é apresentada no **gerado miniaturas** carousel. Esta imagem foi redimensionada pela função, adicionada ao contentor thumbs e transferida pelo cliente de web. 

![Aplicação web publicada no Edge browser](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento do Azure geral
> * Implementar código sem servidor utilizando as funções do Azure
> * Criar uma subscrição de evento de armazenamento de BLOBs na grelha de eventos

Avançar para a parte três da série armazenamento tutorial para saber como proteger o acesso à conta de armazenamento.

> [!div class="nextstepaction"]
> [Acesso seguro aos dados de aplicações na nuvem](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)


+ Para saber mais sobre a grelha de eventos, consulte [uma introdução à grelha de eventos do Azure](overview.md). 
+ Para experimentar outro tutorial que inclui as funções do Azure, consulte [criar uma função que se integra com Azure Logic Apps](..\azure-functions\functions-twitter-email.md). 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md