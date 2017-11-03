---
title: "Efetuar operações em armazenamento de filas do Azure com o PowerShell | Microsoft Docs"
description: "Como realizar operações no armazenamento de filas do Azure com o PowerShell"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: 36eb6db83bb902b35efb8c9666ab06e0c618d602
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Efetuar operações de armazenamento de filas do Azure com o Azure PowerShell

O Armazenamento de Filas do Azure é um serviço para armazenar um grande número de mensagens que podem ser acedidas a partir de qualquer local no mundo através de chamadas autenticadas com HTTP ou HTTPS. Para obter informações detalhadas, consulte [introdução às filas do Azure](storage-queues-introduction.md). Este artigo procedimentos abrange operações de armazenamento de filas comuns. Saiba como:

> [!div class="checklist"]
> * Criar uma fila
> * Obter uma fila
> * Adicionar uma mensagem
> * Ler uma mensagem
> * Eliminar uma mensagem 
> * Eliminar uma fila

Este procedimentos requer o Azure PowerShell versão do módulo 3,6 ou posterior. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-azurerm-ps).

Não existem nenhum cmdlets do PowerShell para o plane de dados para filas. Para efetuar dados operações plane como adicionar uma mensagem, leia uma mensagem e eliminar uma mensagem, terá de utilizar a biblioteca de clientes de armazenamento de .NET como o é exposto no PowerShell. Criar um objeto de mensagem e, em seguida, pode utilizar comandos como AddMessage para executar operações nessa mensagem. Este artigo mostra-lhe como fazê-lo.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Login-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Login-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Obter a lista de localizações

Se não souber qual localização que pretende utilizar, pode listar as localizações disponíveis. Depois de é apresentada a lista, localize aquela que pretende utilizar. Utilizará este exercício **eastus**. Armazene-o na variável **localização** para utilização futura.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Armazene o nome do grupo de recursos numa variável para utilização futura. Neste exemplo, um grupo de recursos denominado *howtoqueuesrg* é criado no *eastus* região.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Criar conta de armazenamento

Criar uma conta de armazenamento para fins gerais standard com a utilização do armazenamento localmente redundante (LRS) [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Obter o contexto da conta de armazenamento que define a conta de armazenamento a ser utilizado. Quando a atuar numa conta de armazenamento, referenciar o contexto em vez de repetidamente fornecer as credenciais.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Criar uma fila

O exemplo seguinte primeiro estabelece uma ligação ao armazenamento do Azure utilizando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e a sua chave de acesso. Em seguida, chama [New-AzureStorageQueue](/powershell/module/azure.storage/new-azurestoragequeue) cmdlet para criar uma fila com o nome 'queuename'.

```powershell
$queueName = "howtoqueue"
$queue = New-AzureStorageQueue –Name $queueName -Context $ctx
```

Para informações sobre as convenções de nomenclatura para o serviço de fila do Azure, consulte [nomenclatura de filas e metadados](http://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Obter uma fila

Pode consultar e obter uma fila específica ou uma lista de todas as filas de uma conta de armazenamento. Os exemplos seguintes demonstram como obter todas as filas na conta do storage e uma fila específica; ambos os comandos utilizam o [Get-AzureStorageQueue](/powershell/module/azure.storage/get-azurestoragequeue) cmdlet.

```powershell
# Retrieve a specific queue
$queue = Get-AzureStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzureStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem para uma fila

Operações com impacto reais mensagens na fila de utilizam a biblioteca de clientes de armazenamento de .NET como exposto no PowerShell. Para adicionar uma mensagem para uma fila, crie uma nova instância do objeto de mensagem, [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) classe. Em seguida, chame o método [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx). Um CloudQueueMessage pode ser criado a partir de uma cadeia (no formato UTF-8) ou uma matriz de bytes.

O exemplo seguinte mostra como adicionar uma mensagem para a fila.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessage($QueueMessage)

# Add two more messages to the queue 
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessage($QueueMessage)
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessage($QueueMessage)
```

Se utilizar o [Explorador de armazenamento do Azure](http://storageexplorer.com), pode ligar à sua conta do Azure e ver as filas na conta de armazenamento e desagregar para uma fila para ver as mensagens na fila. 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Ler uma mensagem da fila, em seguida, elimine-o

As mensagens são lidas por ordem melhor-tente primeiro na primeira fora. Não é garantida. Ao ler a mensagem da fila, torna-se invisível para todos os outros processos observar a fila. Isto garante que, se o código não conseguir processar a mensagem devido uma falha de hardware ou software, outra instância do seu código pode obter a mesma mensagem e tente novamente.  

Isto **tempo limite de invisibilidade** define quanto a mensagem permanece invisível antes de ser novamente disponível para processamento. A predefinição é 30 segundos. 

O código lê uma mensagem da fila em dois passos. Quando chamar o [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) método, obterá a seguinte mensagem na fila. Uma mensagem devolvida por **GetMessage** torna-se invisível para quaisquer outras mensagens de leitura de código desta fila. Para acabar de remover a mensagem da fila, tem de chamar o [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) método. 

No exemplo seguinte, leu as mensagens de três fila, em seguida, aguarde 10 segundos (o tempo limite de invisibilidade). Em seguida, leia as mensagens de três novamente, eliminar as mensagens depois de lê-los ao chamar **DeleteMessage**. Se tentar ler a fila depois das mensagens são eliminadas, $queueMessage será devolvido como NULL.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 

# After 10 seconds, these messages reappear on the queue. 
# Read them again, but delete each one after reading it.
# Delete the message.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
```

## <a name="delete-a-queue"></a>Eliminar uma fila
Para eliminar uma fila e todas as mensagens nela contidas, chame o cmdlet Remove-AzureStorageQueue. O exemplo seguinte mostra como eliminar a fila específica utilizada neste exercício, utilizando o cmdlet Remove-AzureStorageQueue.

```powershell
# Delete the queue 
Remove-AzureStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os recursos que criou neste exercício, remova o grupo de recursos. Isto também elimina todos os recursos contidos dentro do grupo. Neste caso, remove a conta de armazenamento criada e o grupo de recursos em si.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Este artigo procedimentos, aprendeu sobre a gestão de armazenamento de filas básica com o PowerShell, incluindo como:

> [!div class="checklist"]
> * Criar uma fila
> * Obter uma fila
> * Adicionar uma mensagem
> * Ler a mensagem seguinte
> * Eliminar uma mensagem 
> * Eliminar uma fila

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Cmdlets de armazenamento do Microsoft Azure PowerShell
* [Cmdlets do PowerShell do armazenamento](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Explorador de Armazenamento do Microsoft Azure
* O [Explorador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.