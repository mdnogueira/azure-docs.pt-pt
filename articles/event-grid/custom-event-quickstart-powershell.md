---
title: Eventos personalizados do Azure Event Grid com o PowerShell | Microsoft Docs
description: "Utilize o Azure Event Grid e o PowerShell para publicar um tópico e subscrever esse evento."
services: event-grid
keywords: 
author: djrosanova
ms.author: darosa
ms.date: 10/11/2017
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: 89c71194c2ef3c34b3356040c2e252fc09ba09c3
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="create-and-route-custom-events-with-azure-powershell-and-event-grid"></a>Criar e encaminhar eventos personalizados com o Azure PowerShell e o Event Grid

O Azure Event Grid é um serviço de eventos para a cloud. Neste artigo, o Azure PowerShell é utilizado para criar um tópico personalizado, subscrever o tópico e acionar o evento para ver o resultado. Normalmente, os eventos são enviados para um ponto final que responde ao evento, como um webhook ou uma Função do Azure. No entanto, para simplificar este artigo, irá enviar eventos para um URL que apenas recolhe as mensagens. Este URL é criado através de uma ferramenta de terceiros open source chamada [RequestBin](https://requestb.in/).

>[!NOTE]
>**RequestBin** é uma ferramenta open source que não se destina a uma utilização de débito elevado. A utilização da ferramenta aqui é puramente demonstrativa. Se emitir mais do que um evento simultaneamente, não poderá ver todos os eventos na ferramenta.

Quando tiver terminado, verá que os dados do evento foram enviados para um ponto final.

![Dados do evento](./media/custom-event-quickstart-powershell/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Este artigo requer a utilização da versão mais recente do Azure PowerShell. Se precisar de instalar ou atualizar, veja [Instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos do Event Grid são recursos do Azure e têm de ser colocados num grupo de recursos do Azure. Um grupo de recursos é uma coleção lógica na qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).

O exemplo seguinte cria um grupo de recursos com o nome *gridResourceGroup* na localização *westus2*.

```powershell
New-AzureRmResourceGroup -Name gridResourceGroup -Location westus2
```

## <a name="create-a-custom-topic"></a>Criar um tópico personalizado

Um tópico fornece um ponto final definido pelo utilizador no qual publica os eventos. O exemplo seguinte cria o tópico no seu grupo de recursos. Substitua `<topic_name>` por um nome exclusivo para o seu tópico. O nome do tópico deve ser exclusivo, porque este é representado por uma entrada DNS. Na versão anterior, o Event Grid suporta as localizações **westus2** e **westcentralus**.

```powershell
New-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Location westus2 -Name <topic_name>
```

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Antes de subscrever o tópico, vamos criar o ponto final para a mensagem de evento. Em vez de escrever código para responder ao evento, vamos criar um ponto final que recolhe as mensagens, para que possa vê-las. RequestBin é uma ferramenta de terceiros open source que permite criar um ponto final e ver os pedidos que são enviados para o mesmo. Aceda a [RequestBin](https://requestb.in/)e clique em **Criar um RequestBin**.  Copie o URL do bin, porque irá precisar dele quando subscrever o tópico.

## <a name="subscribe-to-a-topic"></a>Subscrever um tópico

Subscreva um tópico para comunicar ao Event Grid os eventos que pretende controlar. O exemplo seguinte subscreve o tópico que criou e transmite o URL do RequestBin como o ponto final para notificação de eventos. Substitua `<event_subscription_name>` por um nome exclusivo para a sua subscrição e `<URL_from_RequestBin>` pelo o valor da secção anterior. Ao especificar um ponto final quando subscrever, o Event Grid processa o encaminhamento de eventos para esse ponto final. Para `<topic_name>`, utilize o valor que criou anteriormente.

```powershell
New-AzureRmEventGridSubscription -EventSubscriptionName <event_subscription_name> -Endpoint <URL_from_RequestBin> -ResourceGroupName gridResourceGroup -TopicName <topic_name>
```

## <a name="send-an-event-to-your-topic"></a>Enviar um evento para o seu tópico

Agora, vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final. Em primeiro lugar, vamos obter o URL e a chave do tópico. Novamente, utilize o nome do tópico de `<topic_name>`.

```powershell
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName gridResourceGroup -Name <topic-name>
```

Para simplificar este artigo, configure dados do evento de exemplo para enviar para o tópico. Normalmente, uma aplicação ou serviço do Azure enviaria os dados do evento. O exemplo seguinte obtém os dados do evento:

```powershell
$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$body = "[{`"id`": `"$eventID`",`"eventType`": `"recordInserted`",`"subject`": `"myapp/vehicles/motorcycles`",`"eventTime`": `"$eventDate`",`"data`":{`"make`": `"Ducati`",`"model`": `"Monster`"}}]"
```

Se visualizar o `$body`, pode ver o evento completo. O elemento `data` do JSON é o payload do evento. Qualquer JSON bem formado pode ir para este campo. Também pode utilizar o campo do assunto para encaminhamento e filtragem avançados.

Agora, envie um evento para o seu tópico.

```powershell
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Acionou o evento e o Event Grid enviou a mensagem para o ponto final que configurou ao subscrever. Navegue para o URL do RequestBin que criou anteriormente. Em alternativa, clique em Atualizar no seu browser RequestBin aberto. Vê o evento que acabou de enviar.

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser continuar a trabalhar com este evento, não limpe os recursos criados neste artigo. Se não quiser continuar, utilize o comando seguinte para eliminar os recursos que criou neste artigo.

```powershell
Remove-AzureRmResourceGroup -Name gridResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como criar tópicos e subscrições de eventos, saiba mais sobre o que o Event Grid pode ajudá-lo a fazer:

- [Sobre o Event Grid](overview.md)
- [Encaminhar eventos de armazenamento de Blobs para um ponto final Web personalizado](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorizar alterações de máquina virtual com o Azure Event Grid e Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Transmitir macrodados em fluxo para um armazém de dados](event-grid-event-hubs-integration.md)
