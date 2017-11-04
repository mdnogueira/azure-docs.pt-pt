---
title: Como utilizar o armazenamento de filas do Ruby | Microsoft Docs
description: "Saiba como utilizar o serviço de fila do Azure para criar e eliminar filas, inserir, obter e eliminar as mensagens. Amostras de escrita em Ruby."
services: storage
documentationcenter: ruby
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 59c2d81b-db9c-46ee-ade2-2f0caae6b1e6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 8fe080aabe3079f571f5979245adfc453dfcd459
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Como utilizar o Armazenamento de filas do Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Descrição geral
Este guia mostra como efetuar cenários comuns utilizando o serviço de armazenamento de filas do Microsoft Azure. Os exemplos são escritos utilizando a API do Azure Ruby.
Os cenários abrangidos incluem **inserir**, **leitura**, **obter**, e **eliminar** fila de mensagens, bem como **criar e eliminar filas**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Criar uma aplicação Ruby
Crie uma aplicação Ruby. Para obter instruções, consulte [Ruby na aplicação Rails Web numa VM do Azure](../../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-access-storage"></a>Configurar a sua aplicação para aceder ao armazenamento
Para utilizar o armazenamento do Azure, terá de transferir e utilizar o pacote do azure Ruby, que inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços REST do storage.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizar RubyGems para obter o pacote
1. Utilize uma interface de linha de comandos, como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix).
2. Escreva "azure de instalação gem" na janela de comando para instalar o gem e dependências.

### <a name="import-the-package"></a>Importar o pacote
Utilize o editor de texto favorito, adicione o seguinte na parte superior do ficheiro Ruby em que pretende utilizar o armazenamento:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento do Azure
O módulo do azure irá ler as variáveis de ambiente **AZURE\_armazenamento\_conta** e **AZURE\_armazenamento\_ACCESS_KEY** de informações necessárias para ligar à sua conta de armazenamento do Azure. Se estas variáveis de ambiente não estiver definidas, tem de especificar as informações de conta antes de utilizar **Azure::QueueService** com o seguinte código:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Para obter estes valores a partir de um clássico ou conta de armazenamento do Resource Manager no portal do Azure:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Navegue para a conta de armazenamento que pretende utilizar.
3. No painel definições à direita, clique em **chaves de acesso**.
4. No painel de chaves de acesso que aparece, verá a chave de acesso 1 e a chave de acesso 2. Pode utilizar qualquer um destes. 
5. Clique no ícone de cópia para copiar a chave para a área de transferência. 

## <a name="how-to-create-a-queue"></a>Como: Criar uma fila
O código seguinte cria um **Azure::QueueService** objeto, que lhe permite trabalhar com as filas.

```ruby
azure_queue_service = Azure::QueueService.new
```

Utilize o **create_queue()** método para criar uma fila com o nome especificado.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Como: Introduzir uma mensagem para uma fila
Para introduzir uma mensagem para uma fila, utilize o **create_message()** método para criar uma nova mensagem e adicioná-lo para a fila.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Como: Pré-visualizar a mensagem seguinte
Pode pré-visualizar a mensagem no início de uma fila sem a remover da fila ao chamar o **peek\_messages()** método. Por predefinição, **peek\_messages()** peeks uma única mensagem. Também pode especificar a quantidade de mensagens que pretende observar.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Como: Anular a mensagem seguinte
Pode remover uma mensagem da fila em dois passos.

1. Quando chamar **lista\_messages()**, obterá a seguinte mensagem numa fila por predefinição. Também pode especificar a quantidade de mensagens que pretende obter. As mensagens devolvidas de **lista\_messages()** torna-se invisível para quaisquer outras código ao ler as mensagens desta fila. Passa o tempo limite de visibilidade em segundos, como um parâmetro.
2. Para acabar de remover a mensagem da fila, também tem de chamar **delete_message()**.

Este processo de dois passos da remoção de uma mensagem garante que quando o seu código não consegue processar uma mensagem devido uma falha de hardware ou software, outra instância do seu código pode obter a mesma mensagem e tente novamente. As chamadas de código **eliminar\_message()** imediatamente após a mensagem ser processada.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como: Alterar os conteúdos de uma mensagem em fila
Pode alterar os conteúdos de uma mensagem no local na fila de espera. O código seguinte utiliza o **update_message()** método para atualizar uma mensagem. O método irá devolver uma cadeia de identificação que contém a receção de pop-a mensagem da fila e um valor de tempo de data UTC representa quando a mensagem vai estar visível na fila.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Como: Mensagens de opções adicionais para Dequeuing
Existem duas formas através das quais pode personalizar a obtenção de mensagens a partir de uma fila.

1. Pode obter um lote de mensagem.
2. Pode definir um tempo limite de invisibilidade superiores ou inferiores, permitindo que o seu código mais ou menos tempo para processar totalmente cada mensagem.

O seguinte código de exemplo utiliza o **lista\_messages()** método para obter 15 mensagens numa chamada. Em seguida, imprime e elimina a cada mensagem. Define também o tempo limite de invisibilidade para cinco minutos para cada mensagem.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Como: Obter o comprimento da fila
Pode obter uma estimativa do número de mensagens em fila. O **obter\_fila\_metadata()** método pede ao serviço de fila para devolver o número aproximado de mensagem e os metadados sobre a fila.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Como: Eliminar uma fila
Para eliminar uma fila e todas as mensagens nela contidas, chame o **eliminar\_queue()** método no objeto de fila.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do armazenamento de filas, siga estas ligações para saber mais sobre as tarefas de armazenamento mais complexas.

* Visite o [blogue da equipa do Storage do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
* Visite o [Azure SDK para Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) repositório no GitHub

Para ver uma comparação entre o serviço de fila de Azure abordadas neste artigo e filas de barramento de serviço de Azure abordado o [como utilizar as filas de barramento de serviço](/develop/ruby/how-to-guides/service-bus-queues/) artigo, consulte [filas do Azure e filas do Service Bus - em comparação com e Contrasted](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)