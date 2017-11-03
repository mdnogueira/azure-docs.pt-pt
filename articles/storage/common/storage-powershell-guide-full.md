---
title: Utilizar o Azure PowerShell com o Storage do Azure | Microsoft Docs
description: Saiba como utilizar os cmdlets do PowerShell do Azure para armazenamento do Azure.
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2017
ms.author: robinsh
ms.openlocfilehash: 1046e407bb4e9d07e91014384e9eba7b0c7020a8
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="using-azure-powershell-with-azure-storage"></a>Utilizar o Azure PowerShell com o Storage do Azure

O Azure PowerShell é utilizado para criar e gerir recursos do Azure, na linha de comandos do PowerShell ou em scripts. Para o armazenamento do Azure, estes cmdlets enquadram-se em duas categorias - plane o controlo e o plane de dados. Os cmdlets do controlo plane são utilizados para gerir a conta de armazenamento – para criar contas de armazenamento, definir as propriedades, eliminar as contas do storage, rodar as chaves de acesso e assim sucessivamente. Os cmdlets de plane dados são utilizados para gerir os dados armazenados *no* a conta de armazenamento. Por exemplo, carregamento blobs, criar partilhas de ficheiros e adicionar mensagens a uma fila.

Este artigo procedimentos abrange operações comuns utilizando os cmdlets de plane de gestão para gerir contas de armazenamento. Saiba como: 

> [!div class="checklist"]
> * Contas de armazenamento de lista
> * Obter uma referência a uma conta de armazenamento existente
> * Criar uma conta de armazenamento 
> * Definir as propriedades da conta de armazenamento
> * Obter e voltar a gerar as chaves de acesso
> * Proteger o acesso à sua conta de armazenamento 
> * Ativar a análise de armazenamento

Este artigo fornece ligações para vários outros artigos do PowerShell para o armazenamento, tais como como ativar e acesso a análise de armazenamento, como utilizar os cmdlets de plane de dados e como aceder a nuvens independentes do Azure como nuvem de China, nuvem alemão e Government Nuvem.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Neste exercício requer o Azure PowerShell versão do módulo 4.4 ou posterior. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps)(Instalar o módulo do Azure PowerShell). 

Para este exercício, pode escrever os comandos numa janela do PowerShell normal ou pode utilizar o [Windows PowerShell Integrated Scripting Environment (ISE)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) e escreva os comandos para um editor, em seguida, testar um ou mais comandos num momento como percorrer os exemplos. Pode destacar linhas que pretende executar e clique em executar seleção para just executar esses comandos.

Para obter mais informações sobre as contas de armazenamento, consulte [introdução ao Storage](storage-introduction.md) e [contas do storage do Azure sobre](storage-create-storage-account.md).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Login-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Login-AzureRmAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Lista as contas de armazenamento na subscrição

Execute o [Get-AzureRMStorageAccount](/powershell/module/azurerm.resources/get-azurermstorageaccount) cmdlet para obter a lista de contas de armazenamento na subscrição atual. 

```powershell
Get-AzureRMStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Obter uma referência a uma conta de armazenamento

Em seguida, precisa de uma referência a uma conta de armazenamento. Pode criar uma nova conta de armazenamento ou obter uma referência a uma conta de armazenamento existente. A secção seguinte mostra os dois métodos. 

### <a name="use-an-existing-storage-account"></a>Utilizar uma conta de armazenamento existente 

Para obter uma conta de armazenamento existente, é necessário o nome do grupo de recursos e o nome da conta de armazenamento. Defina as variáveis para esses dois campos, em seguida, utilizar o [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/Get-AzureRmStorageAccount) cmdlet. 

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
```

Tem agora $storageAccount, que aponta para uma conta de armazenamento existente.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento 

O script seguinte mostra como criar uma conta do storage para fins gerais utilizando [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Depois de criar a conta, obter o contexto, que pode ser utilizado nos comandos subsequentes em vez de especificar a autenticação com cada chamada.

```powershell
# Get list of locations and select one.
Get-AzureRmLocation | select Location 
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 

# Set the name of the storage account and the SKU name. 
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"
    
# Create the storage account.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context. 
$ctx = $storageAccount.Context
```

O script utiliza os seguintes cmdlets do PowerShell: 

*   [Get-AzureRmLocation](/powershell/module/azurerm.storage/Get-AzureRmLocation) – obtém uma lista de localizações válidas. O exemplo utiliza `eastus` para a localização.

*   [Novo-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup) – cria um novo grupo de recursos. Um grupo de recursos é um contentor lógico na qual os recursos do Azure estão implementados e geridos. Ours denomina `teststoragerg`. 

*   [Novo-AzureRmStorageAccount](/powershell/module/azurerm.resources/New-AzureRmStorageAcccount) – cria a conta de armazenamento real. O exemplo utiliza `testpshstorage`.

O nome do SKU indica o tipo de replicação para a conta de armazenamento, tais como LRS (armazenamento localmente redundante). Para obter mais informações sobre a replicação, consulte [replicação do Storage do Azure](storage-redundancy.md).

> [!IMPORTANT]
> O nome da sua conta do storage tem de ser exclusivo no Azure e tem de ser em minúsculas. Para as convenções de nomenclatura e restrições, consulte [nomenclatura e referência de contentores, Blobs e metadados](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
> 

Tem agora uma nova conta de armazenamento e uma referência ao mesmo. 

## <a name="manage-the-storage-account"></a>Gerir a conta de armazenamento

Agora que tem uma referência a uma nova conta de armazenamento ou uma conta de armazenamento existente, a secção seguinte mostra algumas dos comandos que pode utilizar para gerir a sua conta de armazenamento.

### <a name="storage-account-properties"></a>Propriedades da conta de armazenamento

Para alterar as definições para uma conta de armazenamento, utilize [Set-AzureRmStorageAccount](/powershell/module/azurerm.resources/Set-AzureRmStorageAccount). Enquanto não é possível alterar a localização de uma conta de armazenamento ou o grupo de recursos em que reside, pode alterar muitas das outras propriedades. Segue-se algumas das propriedades, que pode alterar a utilizar o PowerShell.

* O **domínio personalizado** atribuída à conta de armazenamento.

* O **etiquetas** atribuída à conta de armazenamento. As etiquetas, muitas vezes, são utilizadas para categorizar os recursos para fins de faturação.

* O **SKU** é a definição de replicação para a conta de armazenamento, tais como LRS para o armazenamento localmente redundante. Por exemplo, poderá alterar do padrão\_LRS para Standard\_GRS ou Standard\_RAGRS. Tenha em atenção que não é possível alterar o Standard ZRS ou Premium LRS para outros SKUs ou alterar outros SKUs para estas. 

* O **camada de acesso** para contas do Blob storage. O valor para a camada de acesso é definido como **frequente** ou **frios**, e permite-lhe minimizar os custos ao selecionar a camada de acesso está alinhada com a forma como utiliza a conta de armazenamento. Para obter mais informações, consulte [frequente, cool e as camadas de armazenamento de arquivo](../blobs/storage-blob-storage-tiers.md).

* A armazenamento encriptação definição de serviço para armazenamento de BLOBs e/ou armazenamento de ficheiros. Para mais informações sobre SSE, consulte [encriptação do serviço de armazenamento](storage-service-encryption.md).

* Permitir apenas o tráfego HTTPS. 

### <a name="manage-the-access-keys"></a>Gerir as chaves de acesso

Uma conta de armazenamento do Azure inclui duas chaves de conta. Para obter as chaves, utilize [Get-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/Get-AzureRmStorageAccountKey). Neste exemplo obtém a primeira chave. Para obter outro, utilize `Value[1]` em vez de `Value[0]`.

```powershell
$storageAccountKey = `
    (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Para voltar a gerar a chave, utilize [New-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccountKey). 

```powershell
New-AzureRmStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1 
```

Para voltar a gerar a chave de outra, utilize `key2` como o nome da chave em vez de `key1`.

Voltar a gerar uma das suas chaves e, em seguida, obtenha-lo novamente para ver o novo valor.

> [!NOTE] 
> Deve efetuar o planeamento cuidado antes de voltar a gerar a chave para uma conta de armazenamento de produção. Regenerar chaves de um ou ambos os irá invalidar o acesso para qualquer aplicação utilizando a chave que foi regenerada. Para obter mais informações, consulte [chaves de acesso de armazenamento Regenate](storage-create-storage-account.md#regenerate-storage-access-keys).


### <a name="delete-a-storage-account"></a>Eliminar uma conta do Storage 

Para eliminar uma conta de armazenamento, utilize [Remove-AzureRmStorageAccount](/powershell/module/azurerm.storage/Remove-AzureRmStorageAccount).

```powershell
Remove-AzureRmStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Quando elimina uma conta de armazenamento, todos os recursos armazenados na conta são eliminados bem. Se eliminar uma conta acidentalmente, contacte o suporte imediatamente e abrir um pedido para restaurar a conta de armazenamento. Não é garantida a recuperação dos seus dados, mas por vezes, funciona. Não crie uma nova conta de armazenamento com o mesmo nome que o antigo até que o pedido de suporte foi resolvido. 
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Proteger a sua conta de armazenamento utilizando as VNets e as firewalls

Por predefinição, todas as contas de armazenamento podem ser acedidas por qualquer rede que tenha acesso à internet. No entanto, pode configurar regras de rede para permitir apenas aplicações de redes virtuais específicas aceder a uma conta de armazenamento. Para obter mais informações, consulte [configurar Firewalls de armazenamento do Azure e as redes virtuais](storage-network-security.md). 

O artigo mostra como gerir estas definições com os seguintes cmdlets do PowerShell:
* [AzureRmStorageAccountNetworkRule adicionar](/powershell/module/AzureRM.Storage/Add-AzureRmStorageAccountNetworkRule)
* [Atualização AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset)
* [Remover AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.storage/remove-azurermstorage-account-networkrule)

## <a name="use-storage-analytics"></a>Utilize a análise de armazenamento  

[Análise de armazenamento do Azure](storage-analytics.md) consiste [as métricas do Storage Analytics](/rest/api/storageservices/about-storage-analytics-metrics) e [o registo de análise do armazenamento](/rest/api/storageservices/about-storage-analytics-logging). 

**As métricas do Storage Analytics** é utilizada para recolher métricas para as contas do storage do Azure que pode utilizar para monitorizar o estado de funcionamento de uma conta de armazenamento. Métricas podem ser ativadas para blobs, ficheiros, tabelas e filas.

**O registo de análise do armazenamento** acontece do lado do servidor e permite-lhe para detalhes de registos de pedidos com êxito ou falhados à sua conta de armazenamento. Estes registos permitem-lhe ver os detalhes de leitura, escrita e eliminação operações contra as tabelas, filas e blobs, bem como as razões para pedidos falhados. O registo não está disponível para ficheiros do Azure.

Pode configurar a monitorização utilizando o [portal do Azure](https://portal.azure.com), PowerShell ou através de programação utilizando a biblioteca de clientes de armazenamento. 

> [!NOTE]
> Pode ativar a análise minutos com o PowerShell. Esta capacidade não está disponível no portal.
>

* Para saber como ativar e ver os dados de métricas de armazenamento com o PowerShell, consulte [dados de métricas de métricas e visualização de armazenamento do Azure de ativação](storage-enable-and-view-metrics.md#how-to-enable-metrics-using-powershell).

* Para saber como ativar e obter dados do registo de armazenamento com o PowerShell, consulte [como ativar o registo de armazenamento com o PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#how-to-enable-storage-logging-using-powershell) e [localizar os dados de registo registo armazenamento](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#finding-your-storage-logging-log-data).

* Para obter informações detalhadas sobre como utilizar as métricas do Storage e o registo de armazenamento para resolver problemas de armazenamento, consulte [monitorização, Diagnosing e resolução de problemas de armazenamento do Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Gerir os dados na conta de armazenamento

Agora que sabe como gerir a sua conta de armazenamento com o PowerShell, pode utilizar os seguintes artigos para saber como aceder a dados de objetos na conta de armazenamento.

* [Como gerir os blobs com o PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Como gerir ficheiros com o PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Como gerir filas com o PowerShell](../queues/storage-powershell-how-to-use-queues.md)

## <a name="azures-independently-deployed-clouds"></a>Nuvens de implementadas de forma independente do Azure

A maioria das pessoas utilizam nuvem pública do Azure para as suas implementações do Azure global. Existem também algumas implementações do Microsoft Azure por motivos de soberania dos independentes e assim sucessivamente. Estas implementações independentes são referidas como "ambientes". Estes são os ambientes disponíveis:

* [Nuvem do Azure Government](https://azure.microsoft.com/features/gov/)
* [Nuvem China Azure operado pela 21Vianet na China](http://www.windowsazure.cn/)
* [Nuvem alemão do Azure](../../germany/germany-welcome.md)

Para obter informações sobre como aceder a estes nuvens e o seu armazenamento com o PowerShell, consulte [gerir o armazenamento em nuvens independentes do Azure com o PowerShell](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se tiver criado um novo grupo de recursos e uma conta de armazenamento para este exercício, yous pode remover todos os recursos que criou ao remover o grupo de recursos. Isto também elimina todos os recursos contidos dentro do grupo. Neste caso, remove a conta de armazenamento criada e o grupo de recursos em si.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Passos Seguintes

Este artigo procedimentos abrange operações comuns utilizando os cmdlets de plane de gestão para gerir contas de armazenamento. Aprendeu a: 

> [!div class="checklist"]
> * Contas de armazenamento de lista
> * Obter uma referência a uma conta de armazenamento existente
> * Criar uma conta de armazenamento 
> * Definir as propriedades da conta de armazenamento
> * Obter e voltar a gerar as chaves de acesso
> * Proteger o acesso à sua conta de armazenamento 
> * Ativar a análise de armazenamento

Este artigo também fornecidos referências a outros artigos, tais como como gerir os objetos de dados, como ativar a análise de armazenamento e como aceder a nuvens independentes do Azure como nuvem China, alemão nuvem e nuvem Government. Seguem-se alguns artigos e recursos para referência mais relacionados:

* [Armazenamento controlo plane cmdlets do Azure PowerShell](/powershell/module/AzureRM.Storage/)
* [Dados de armazenamento do Azure plane cmdlets do PowerShell](/powershell/module/azure.storage/)
* [Referência do Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)