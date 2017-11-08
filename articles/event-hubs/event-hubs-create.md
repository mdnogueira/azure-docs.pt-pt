---
title: Criar um hub de eventos do Azure | Microsoft Docs
description: "Criar um espaço de nomes de Event Hubs do Azure e um hub de eventos no portal do Azure"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: ff99e327-c8db-4354-9040-9c60c51a2191
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: sethm
ms.openlocfilehash: e444c4505d4744c95e08c4ef0d33566356785c81
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Criar um espaço de nomes de Event Hubs e um hub de eventos no portal do Azure

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes de Event Hubs
1. Inicie sessão no [portal do Azure][Azure portal]e clique em **crie um recurso** na parte superior esquerda do ecrã.
1. Clique em **Internet das coisas**e, em seguida, clique em **Event Hubs**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)
1. No **criar espaço de nomes**, introduza um nome de espaço de nomes. O sistema verifica imediatamente a disponibilidade do nome.
   
    ![](./media/event-hubs-create/create-event-hub1.png)
1. Após se certificar de que o espaço de nomes está disponível, selecione o escalão de preço (Básico ou Standard). Escolha também uma subscrição do Azure, um grupo de recursos e a localização na qual pretende criar o recurso. 
1. Clique em **Criar** para criar o espaço de nome. Poderá ter de aguardar alguns minutos para que o sistema totalmente aprovisionar os recursos.
2. Na lista de espaços de nomes portal, clique o espaço de nomes criado recentemente.
2. Clique em **políticas de acesso partilhado**e, em seguida, clique em **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

3. Clique no botão copiar para copiar a cadeia de ligação **RootManageSharedAccessKey** para a área de transferência. Guarde esta cadeia de ligação numa localização temporária, como o Notepad, para utilizar mais tarde.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Criar um hub de eventos

1. Na lista de espaço de nomes de Event Hubs, clique o espaço de nomes criado recentemente.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. No painel de espaço de nomes, clique em **Hubs de Eventos**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

1. Na parte superior do painel, clique em **Adicionar Hub de Eventos**.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
1. Escreva um nome para o hub de eventos, em seguida, clique em **criar**.
   
    ![](./media/event-hubs-create/create-event-hub5.png)

O hub de eventos está agora criado e tem as cadeias de ligação que terá de enviar e receber eventos.

## <a name="next-steps"></a>Passos seguintes
Para mais informações sobre os Event Hubs, visite estas ligações:

* [Descrição geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Descrição geral da API dos Hubs de Eventos](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/