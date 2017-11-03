---
title: Gerir o armazenamento de independente do Azure nuvens a utilizar o Azure PowerShell | Microsoft Docs
description: "Gerir o armazenamento na nuvem de China, nuvem pública e, em alemão em nuvem com o Azure PowerShell"
services: storage
documentationcenter: na
author: robinsh
manager: timlt
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: robinsh
ms.openlocfilehash: 08e1af929d7ddc30c7dc149f6305ca1ca0bc22ae
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Gerir o armazenamento em nuvens independentes do Azure com o PowerShell

A maioria das pessoas utilizam nuvem pública do Azure para as suas implementações do Azure global. Existem também algumas implementações do Microsoft Azure por motivos de soberania dos independentes e assim sucessivamente. Estas implementações independentes são referidas como "ambientes". A lista seguinte fornece detalhes sobre as nuvens independentes atualmente disponíveis.

* [Nuvem do Azure Government](https://azure.microsoft.com/features/gov/)
* [Nuvem China Azure operado pela 21Vianet na China](http://www.windowsazure.cn/)
* [Nuvem alemão do Azure](../../germany/germany-welcome.md)

## <a name="using-an-independent-cloud"></a>Utilizar uma nuvem independente 

Para utilizar o armazenamento do Azure de uma das nuvens independentes, estabelece ligação dessa nuvem em vez de público do Azure. Para utilizar um da nuvens independentes em vez de público do Azure:

* Especificar o *ambiente* para estabelecer ligação.
* Determinar e utilizar as regiões disponíveis.
* Utilize o sufixo de ponto final correto, que é diferente do público do Azure.

Os exemplos necessitam do Azure PowerShell versão do módulo 4.4.0 ou posterior. Na janela do PowerShell, execute `Get-Module -ListAvailable AzureRM` para localizar a versão. Se nada estiver listado, ou terá de atualizar, consulte [módulo Azure PowerShell instalar](/powershell/azure/install-azurerm-ps). 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Execute o [Get-AzureEnvironment](/powershell/module/azure/Get-AzureRmEnvironment) cmdlet para ver os ambientes do Azure disponíveis:
   
```powershell
Get-AzureRmEnvironment
```

Inicie sessão na sua conta que tenha acesso à nuvem para o qual pretende ligar e configurar o ambiente. Este exemplo mostra como iniciar sessão numa conta que utiliza a nuvem do Azure Government.   

```powershell
Login-AzureRmAccount –Environment AzureUSGovernment
```

Para aceder à nuvem China, utilize o ambiente **AzureChinaCloud**. Para aceder à nuvem alemão, utilize **AzureGermanCloud**.

Neste momento, se precisar de lista de localizações para criar uma conta de armazenamento ou outro recurso, pode consultar as localizações disponíveis para a nuvem selecionada utilizando [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation).

```powershell
Get-AzureRmLocation | select Location, DisplayName
```

A tabela seguinte mostra as localizações para a nuvem em alemão.

|Localização | displayName |
|----|----|
| germanycentral | Alemanha Central|
| germanynortheast | Alemanha Nordeste | 


## <a name="endpoint-suffix"></a>Sufixo de ponto final

O sufixo de ponto final de cada um destes ambientes é diferente do ponto final público do Azure. Por exemplo, é o sufixo de ponto final do blob público do Azure para **w**. Para a nuvem do Governo dos EUA, o sufixo de ponto final do blob é **blob.core.usgovcloudapi.net**. 

### <a name="get-endpoint-using-get-azurermenvironment"></a>Obter o ponto final utilizando Get AzureRMEnvironment 

Obter o sufixo de ponto final utilizando [Get-AzureRMEnvironment](/powershell/module/azurerm.profile/get-azurermenvironment). O ponto final está a *StorageEndpointSuffix* propriedade do ambiente. Os fragmentos de código seguintes mostram como fazê-lo. Todos estes comandos devolvem algo "core.cloudapp.net" ou "core.cloudapi.de", etc. Acrescente esta para o serviço de armazenamento para aceder a esse serviço. Por exemplo, "queue.core.cloudapi.de" irá aceder ao serviço de fila na nuvem alemão.

Este fragmento de código obtém todos os ambientes e o sufixo de ponto final para cada um deles.

```powershell
Get-AzureRmEnvironment | select Name, StorageEndpointSuffix 
```

Este comando devolve os seguintes resultados.

| Nome| StorageEndpointSuffix|
|----|----|
|AzureChinaCloud | Core.chinacloudapi.CN|
| AzureCloud | Core.Windows.NET |
| AzureGermanCloud | Core.cloudapi.de|
| AzureUSGovernment | Core.usgov.cloudapi.NET |


Para obter todas as propriedades para o ambiente especificado, chame **Get-AzureRmEnvironment** e especifique o nome da nuvem. Este fragmento de código devolve uma lista de propriedades; Procure **StorageEndpointSuffix** na lista. O exemplo seguinte é para a nuvem alemão.

```powershell
Get-AzureRmEnvironment -Name AzureGermanCloud 
```

Os resultados são semelhantes ao seguinte:

|Nome da propriedade|Valor|
|----|----|
| Nome | AzureGermanCloud |
| EnableAdfsAuthentication | Falso |
| ActiveDirectoryServiceEndpointResourceI | http://Management.Core.cloudapi.de/ |
| GalleryURL | https://Gallery.cloudapi.de/ |
| ManagementPortalUrl | https://portal.microsoftazure.de/ | 
| ServiceManagementUrl | https://Manage.Core.cloudapi.de/ |
| PublishSettingsFileUrl| https://Manage.microsoftazure.de/publishsettings/Index |
| ResourceManagerUrl | http://Management.microsoftazure.de/ |
| SqlDatabaseDnsSuffix | . database.cloudapi.de |
| **StorageEndpointSuffix** | Core.cloudapi.de |
| ... | ... | 

Para obter apenas a propriedade de sufixo de ponto final armazenamento, obtenha a nuvem específica e voltar a pedir apenas que uma propriedade.

```powershell
$environment = Get-AzureRmEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

Esta ação devolve as informações seguintes.

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>Obter o ponto final de uma conta de armazenamento

Também pode examinar as propriedades de uma conta de armazenamento para obter os pontos finais. Isto pode ser útil se já estiver a utilizar uma conta do storage no script do PowerShell; apenas pode obter o ponto final que precisa. 

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

Para uma conta de armazenamento na nuvem pública, esta ação devolve o seguinte: 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Depois de definir o ambiente

De aqui daqui, pode utilizar o mesmo PowerShell utilizado para gerir as contas de armazenamento e aceder a plane de dados, conforme descrito no artigo [utilizar o Azure PowerShell com o Storage do Azure](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se tiver criado um novo grupo de recursos e uma conta de armazenamento para este exercício, pode remover todos os recursos ao remover o grupo de recursos. Isto também elimina todos os recursos contidos dentro do grupo. Neste caso, remove a conta de armazenamento criada e o grupo de recursos em si.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

* [Persistentes inícios de sessão do utilizador entre sessões do PowerShell](/powershell/azure/context-persistence)
* [Armazenamento do Azure Government](../../azure-government/documentation-government-services-storage.md)
* [Guia para programadores do Microsoft Azure Government](../../azure-government/documentation-government-developer-guide.md)
* [Notas do Programador de aplicações do Azure China](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Documentação de Datacenters do Azure](../../germany/germany-welcome.md)