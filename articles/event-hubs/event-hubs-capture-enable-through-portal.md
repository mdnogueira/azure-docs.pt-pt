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
ms.date: 06/28/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 1f3d373944b909db290f6cf2da7bf12a8a00e1c5
ms.contentlocale: pt-pt
ms.lasthandoff: 06/28/2017


---

# <a name="enable-event-hubs-capture-using-the-azure-portal"></a>Ativar a funcionalidade de Captura de Hubs de Eventos no portal do Azure

Pode configurar a captura no momento de criação do hub de eventos através do [portal do Azure](https://portal.azure.com). Para ativar a Captura, clique no botão **Ativar** no painel do portal **Criar Hub de Eventos**. Pode configurar uma conta de armazenamento e um contentor ao clicar na secção **Contentor** do painel. Uma vez que a Captura dos Hubs de Eventos utiliza a autenticação de serviço a serviço com o armazenamento, não é necessário especificar uma cadeia de ligação do armazenamento. O selecionador de recursos seleciona automaticamente a URI do recurso da sua conta de armazenamento. Se utilizar o Azure Resource Manager, deve fornecer essa URI explicitamente como uma cadeia de carateres.

A janela de tempo predefinida é de 5 minutos. O valor mínimo é 1, o máximo é 15. A janela **Tamanho** tem um intervalo de 10 a 500 MB.

![][1]

## <a name="adding-capture-to-an-existing-event-hub"></a>Adicionar a Captura a um hub de eventos existente

Pode configurar a Captura em hubs de eventos existentes que se encontram num espaço de nomes de Hubs de Eventos. A funcionalidade não está disponível para os espaços de nomes mais antigo do tipo **Mensagens** ou **Combinado**. Para ativar a Captura num hub de eventos existente ou alterar as definições de Captura, clique no espaço de nomes para carregar o painel **Essenciais** e, em seguida, clique no hub de eventos que pretende ativar ou cuja definição de Captura pretende alterar. Por último, clique na secção **Propriedades** do painel aberto, tal como mostrado na imagem seguinte:

![][2]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png

## <a name="next-steps"></a>Passos seguintes

Também pode configurar uma Captura de Hubs de Eventos através dos modelos do Azure Resource Manager. Para obter mais informações, consulte [Enable Capture using an Azure Resource Manager template](event-hubs-resource-manager-namespace-event-hub-enable-capture.md) (Ativar a Captura através de um modelo do Azure Resource Manager).

