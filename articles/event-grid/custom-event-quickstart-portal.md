---
title: Eventos personalizados do Azure Event Grid com o portal do Azure | Microsoft Docs
description: "Utilize o Azure Event Grid e o PowerShell para publicar um tópico e subscrever esse evento."
services: event-grid
keywords: 
author: djrosanova
ms.author: darosa
ms.date: 10/11/2017
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: 0fe498b7b6dcf59bc5caef8ff5a40053e0498f85
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="create-and-route-custom-events-with-the-azure-portal-and-event-grid"></a>Criar e encaminhar eventos personalizados com o portal do Azure e o Event Grid

O Azure Event Grid é um serviço de eventos para a cloud. Neste artigo, o portal do Azure é utilizado para criar um tópico personalizado, subscrever o tópico e acionar o evento para ver o resultado. Normalmente, os eventos são enviados para um ponto final que responde ao evento, como um webhook ou uma Função do Azure. No entanto, para simplificar este artigo, irá enviar eventos para um URL que apenas recolhe as mensagens. Este URL é criado através de uma ferramenta de terceiros open source chamada [RequestBin](https://requestb.in/).

>[!NOTE]
>**RequestBin** é uma ferramenta open source que não se destina a uma utilização de débito elevado. A utilização da ferramenta aqui é puramente demonstrativa. Se emitir mais do que um evento simultaneamente, não poderá ver todos os eventos na ferramenta.

Quando tiver terminado, verá que os dados do evento foram enviados para um ponto final.

![Dados do evento](./media/custom-event-quickstart-portal/request-result.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos do Event Grid são recursos do Azure e têm de ser colocados num grupo de recursos do Azure. Um grupo de recursos é uma coleção lógica na qual os recursos do Azure são implementados e geridos.

1. Na navegação à esquerda, selecione **Grupos de Recursos**. Em seguida, selecione **Adicionar**.

   ![Criar grupo de recursos](./media/custom-event-quickstart-portal/create-resource-group.png)

1. Defina o nome do grupo de recursos como *gridResourceGroup* e a localização como *westus2*. Selecione **Criar**.

   ![Fornecer valores do grupo de recursos](./media/custom-event-quickstart-portal/provide-resource-group-values.png)

## <a name="create-a-custom-topic"></a>Criar um tópico personalizado

Um tópico fornece um ponto final definido pelo utilizador no qual publica os eventos. 

1. Para criar um tópico no grupo de recursos, selecione **Mais serviços** e procure *Event Grid*. Selecione **Tópicos do Event Grid** nas opções disponíveis.

   ![Criar um tópico do Event Grid](./media/custom-event-quickstart-portal/create-event-grid-topic.png)

1. Selecione **Adicionar**.

   ![Adicionar um tópico do Event Grid](./media/custom-event-quickstart-portal/add-topic.png)

1. Indique um nome para o tópico. O nome do tópico deve ser exclusivo, porque este é representado por uma entrada DNS. Na versão anterior, o Event Grid suporta as localizações **westus2** e **westcentralus**. Selecione o grupo de recursos que criou anteriormente. Selecione **Criar**.

   ![Fornecer valores do tópico do Event Grid](./media/custom-event-quickstart-portal/provide-topic-values.png)

1. Após a criação do tópico, selecione **Atualizar** para ver o tópico.

   ![Ver um tópico do Event Grid](./media/custom-event-quickstart-portal/see-topic.png)

## <a name="create-a-message-endpoint"></a>Criar um ponto final de mensagem

Antes de subscrever o tópico, vamos criar o ponto final para a mensagem de evento. Em vez de escrever código para responder ao evento, vamos criar um ponto final que recolhe as mensagens, para que possa vê-las. RequestBin é uma ferramenta de terceiros open source que permite criar um ponto final e ver os pedidos que são enviados para o mesmo. Aceda a [RequestBin](https://requestb.in/)e clique em **Criar um RequestBin**.  Copie o URL do bin, porque irá precisar dele quando subscrever o tópico.

## <a name="subscribe-to-a-topic"></a>Subscrever um tópico

Subscreva um tópico para comunicar ao Event Grid os eventos que pretende controlar. 

1. Para criar uma subscrição do Event Grid, selecione novamente **Mais Serviços** e procure *Event Grid*. Selecione **Subscrições do Event Grid** nas opções disponíveis.

   ![Criar uma subscrição do Event Grid](./media/custom-event-quickstart-portal/create-subscription.png)

1. Selecione **+ Subscrição de Eventos**.

   ![Adicionar uma subscrição do Event Grid](./media/custom-event-quickstart-portal/add-subscription.png)

1. Indique um nome exclusivo para a subscrição de eventos. Para o tipo de tópico, selecione **Tópicos do Event Grid**. Para a instância, selecione o tópico personalizado que criou. Forneça o URL de RequestBin como ponto final da notificação de eventos. Quando terminar de fornecer valores, selecione **Criar**.

   ![Fornecer um valor da subscrição do Event Grid](./media/custom-event-quickstart-portal/provide-subscription-values.png)

Agora, vamos acionar um evento para ver como o Event Grid distribui a mensagem para o ponto final. Para simplificar este artigo, utilize o Cloud Shell para enviar os dados do evento de exemplo para o tópico. Normalmente, uma aplicação ou serviço do Azure enviaria os dados do evento.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="send-an-event-to-your-topic"></a>Enviar um evento para o seu tópico

Em primeiro lugar, vamos obter o URL e a chave do tópico. Utilize o nome do tópico para `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

O exemplo seguinte obtém os dados do evento de exemplo:

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

Se fizer `echo "$body"`, pode ver o evento completo. O elemento `data` do JSON é o payload do evento. Qualquer JSON bem formado pode ir para este campo. Também pode utilizar o campo do assunto para encaminhamento e filtragem avançados.

CURL é um utilitário que efetua os pedidos HTTP. Neste artigo, utilizamos CURL para enviar o evento para o nosso tópico. 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
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

Se quiser continuar a trabalhar com este evento, não limpe os recursos criados neste artigo. Se não quiser continuar, elimine os recursos que criou neste artigo.

Selecione o grupo de recursos e selecione **Eliminar grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como criar tópicos e subscrições de eventos, saiba mais sobre o que o Event Grid pode ajudá-lo a fazer:

- [Sobre o Event Grid](overview.md)
- [Encaminhar eventos de armazenamento de Blobs para um ponto final Web personalizado](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorizar alterações de máquina virtual com o Azure Event Grid e Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Transmitir macrodados em fluxo para um armazém de dados](event-grid-event-hubs-integration.md)
