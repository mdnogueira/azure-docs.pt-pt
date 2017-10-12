---
title: "Ativação da Captura de Hubs de Eventos do Azure no portal | Microsoft Docs"
description: Ative a funcionalidade de Captura de Hubs de Eventos no portal do Azure.
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2017
ms.author: sethm
ms.openlocfilehash: 4a4ab1ee022b6b33d35217df916d01f32e04d3a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-event-hubs-capture-using-the-azure-portal"></a>Ativar a funcionalidade de Captura de Hubs de Eventos no portal do Azure

A [Captura de Hubs de Eventos][capture-overview] do Azure permite a transmissão automática de dados nos Hubs de Eventos para um [armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) ou [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) à sua escolha.

Pode configurar a captura no momento de criação do hub de eventos através do [portal do Azure](https://portal.azure.com). Pode optar por capturar os dados para um contentor de [armazenamento de Blobs](https://azure.microsoft.com/services/storage/blobs/) do Azure ou para uma conta do [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Para obter mais informações, veja a [Descrição geral de Captura de Hubs de Eventos][capture-overview].

## <a name="capture-data-to-an-azure-storage-account"></a>Capturar dados para uma conta de Armazenamento do Azure  

Quando criar um hub de eventos, pode ativar a Captura clicando no botão **Ativar** no ecrã do portal **Criar Hub de Eventos**. Em seguida, especifique uma Conta de Armazenamento e um contentor clicando em **Armazenamento do Azure** na caixa **Capturar Fornecedor**. Uma vez que a Captura dos Hubs de Eventos utiliza a autenticação de serviço a serviço com o armazenamento, não é necessário especificar uma cadeia de ligação do armazenamento. O selecionador de recursos seleciona automaticamente a URI do recurso da sua conta de armazenamento. Se utilizar o Azure Resource Manager, deve fornecer essa URI explicitamente como uma cadeia de carateres.

A janela de tempo predefinida é de 5 minutos. O valor mínimo é 1, o máximo é 15. A janela **Tamanho** tem um intervalo de 10 a 500 MB.

![][1]

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Capturar dados para uma conta do Azure Data Lake Store

Para capturar os dados para o Azure Data Lake Store, crie uma conta do Data Lake Store e um hub de eventos:

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Criar uma conta e pastas do Azure Data Lake Store

1. Crie uma conta do Data Lake Store, seguindo as instruções em [Introdução ao Azure Data Lake Store com o portal do Azure](../data-lake-store/data-lake-store-get-started-portal.md).
2. Siga as instruções da secção [Assign permissions to Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) (Atribuir permissões aos Hubs de Eventos) para criar uma pasta na conta do Data Lake Store na qual quer capturar os dados dos Hubs de Eventos e atribua permissões a esta serviço, para que possa escrever dados na sua conta do Data Lake Store.  

### <a name="create-an-event-hub"></a>Criar um hub de eventos

1. Tenha em atenção que o hub de eventos tem de estar na mesma subscrição do Azure como o Azure Data Lake Store que acabou de criar. Crie o hub de eventos, clicando no botão **Ativar** em **Captura** na página do portal **Criar Hub de Eventos**. 
2. Na página do portal **Criar Hub de Eventos**, selecione **Azure Data Lake Store** a partir da caixa **Capturar Fornecedor**.
3. Em **Selecionar Data Lake Store**, especifique a conta do Data Lake Store que criou anteriormente e no campo **Caminho do Data Lake**, introduza o caminho para a pasta de dados que criou.

    ![][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Adicionar ou configurar a Captura de um hub de eventos existente

Pode configurar a Captura em hubs de eventos existentes que se encontram num espaço de nomes de Hubs de Eventos. Para ativar a Captura num hub de eventos existente ou alterar as definições de Captura, clique no espaço de nomes para carregar o ecrã **Essenciais** e, em seguida, clique no hub de eventos que pretende ativar ou cuja definição de Captura pretende alterar. Por último, clique na secção **Propriedades** da página aberta e, em seguida, edite as definições de Captura, tal como mostrado nas imagens seguintes:

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

![][2]

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

![][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a captura dos Hubs de Eventos ao ler a [Descrição geral de Captura de Hubs de Eventos][capture-overview].
- Também pode configurar uma Captura de Hubs de Eventos através dos modelos do Azure Resource Manager. Para obter mais informações, consulte [Enable Capture using an Azure Resource Manager template](event-hubs-resource-manager-namespace-event-hub-enable-capture.md) (Ativar a Captura através de um modelo do Azure Resource Manager).
- [Introdução ao Azure Data Lake Store com o portal do Azure](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md