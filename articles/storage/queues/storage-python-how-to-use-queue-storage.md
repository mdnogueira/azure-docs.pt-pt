---
title: Como utilizar o armazenamento de filas do Python | Microsoft Docs
description: "Saiba como utilizar o serviço de fila do Azure do Python para criar e eliminar filas, inserir, obter e eliminar as mensagens."
services: storage
documentationcenter: python
author: tamram
manager: timlt
editor: tysonn
ms.assetid: cc0d2da2-379a-4b58-a234-8852b4e3d99d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: c7976c01436b1c30880bfd4c57cb97f72a4f48b0
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-use-queue-storage-from-python"></a>Como utilizar o Armazenamento de filas do Python
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra como efetuar cenários comuns utilizando o serviço de armazenamento de filas do Azure. Os exemplos são escritos no Python e utilize o [SDK de armazenamento do Microsoft Azure para Python]. Os cenários abrangidos incluem **inserir**, **leitura**, **obter**, e **eliminar** fila de mensagens, bem como **criar e eliminar filas**. Para obter mais informações sobre as filas, consulte a secção [passos].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Transfira e instale o armazenamento do Azure SDK para Python

SDK de armazenamento do Azure para Python requer o Python 2.7, 3.3, 3.4, 3.5 ou 3.6 e é apresentada no 4 diferentes pacotes: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` e `azure-storage-queue`. Neste tutorial, vamos utilizar `azure-storage-queue` pacote.
 
### <a name="install-via-pypi"></a>Instalar através de PyPi

Para instalar através do índice de pacote do Python (PyPI), escreva:

```bash
pip install azure-storage-queue
```


> [!NOTE]
> Se estiver a atualizar o armazenamento do Azure SDK para Python versão 0.36 ou anterior, primeiro terá de desinstalar com `pip uninstall azure-storage` como vamos já não lançar o SDK de armazenamento para o Python num pacote único.
> 
> 

Para métodos de instalação alternativa, visite o [SDK de armazenamento do Azure para Python no Github](https://github.com/Azure/azure-storage-python/).

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila
O **QueueService** objeto permite-lhe trabalhar com as filas. O código seguinte cria um **QueueService** objeto. Adicione o seguinte perto da parte superior de qualquer ficheiro de Python no qual pretende aceder programaticamente ao Storage do Azure:

```python
from azure.storage.queue import QueueService
```

O código seguinte cria um **QueueService** utilizando a chave de conta e o nome da conta de armazenamento de objeto. Substitua 'myaccount' e 'mykey' com o nome da conta e a chave.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Introduzir uma mensagem para uma fila
Para introduzir uma mensagem para uma fila, utilize o **colocar\_mensagem** método para criar uma nova mensagem e adicioná-lo para a fila.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>Como: Pré-visualizar a mensagem seguinte
Pode pré-visualizar a mensagem no início de uma fila sem a remover da fila ao chamar o **peek\_mensagens** método. Por predefinição, **peek\_mensagens** peeks uma única mensagem.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>Como: Anular mensagens
O código remove uma mensagem da fila em dois passos. Quando chamar **obter\_mensagens**, obterá a seguinte mensagem numa fila por predefinição. Uma mensagem devolvida por **obter\_mensagens** torna-se invisível para quaisquer outras código ao ler as mensagens desta fila. Por predefinição, esta mensagem permanece invisível durante 30 segundos. Para acabar de remover a mensagem da fila, também tem de chamar **eliminar\_mensagem**. Este processo de dois passos da remoção de uma mensagem garante que quando o seu código não consegue processar uma mensagem devido uma falha de hardware ou software, outra instância do seu código pode obter a mesma mensagem e tente novamente. As chamadas de código **eliminar\_mensagem** imediatamente após a mensagem ser processada.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila.
Em primeiro lugar, pode obter um lote de mensagens (até 32). Em segundo lugar, pode definir um tempo limite de invisibilidade superior ou inferior, dando mais ou menos tempo ao código para processar totalmente cada mensagem. O seguinte código de exemplo utiliza o **obter\_mensagens** método para obter 16 mensagens numa chamada. Em seguida, processa cada mensagem utilizando um ciclo for. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem.

```python
messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)        
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar os conteúdos de uma mensagem em fila
Pode alterar os conteúdos de uma mensagem no local na fila de espera. Se a mensagem representa uma tarefa de trabalho, pode utilizar esta funcionalidade para atualizar o estado da tarefa de trabalho. O código seguinte utiliza o **atualizar\_mensagem** método para atualizar uma mensagem. O tempo limite de visibilidade estiver definido como 0, que significa a mensagem é apresentada imediatamente e o conteúdo é atualizado.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila
Pode obter uma estimativa do número de mensagens numa fila. O **obter\_fila\_metadados** método pede ao serviço de fila para devolver os metadados sobre a fila e o **approximate_message_count**. O resultado é aproximado apenas porque as mensagens podem ser adicionadas ou removidas depois de ter o serviço fila responde ao seu pedido.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>Como: Eliminar uma fila
Para eliminar uma fila e todas as mensagens nela contidas, chame o **eliminar\_fila** método.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do armazenamento de filas, siga estas ligações para saber mais.

* [Centro para Programadores do Python](/develop/python/)
* [API REST dos Serviços do Armazenamento do Azure](http://msdn.microsoft.com/library/azure/dd179355)
* [Blogue da Equipa de Armazenamento do Azure]
* [SDK de armazenamento do Microsoft Azure para Python]

[Blogue da Equipa de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[SDK de armazenamento do Microsoft Azure para Python]: https://github.com/Azure/azure-storage-python