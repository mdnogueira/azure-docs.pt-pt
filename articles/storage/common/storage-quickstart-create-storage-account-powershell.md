---
title: "Início rápido do Azure - criar uma conta de armazenamento utilizando o PowerShell | Microsoft Docs"
description: Rapidamente aprender a criar uma nova conta de armazenamento com o PowerShell
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/29/2017
ms.author: robinsh
ms.openlocfilehash: c9175cce0cb93e73009fb8d751e54f631603d482
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-storage-account-using-powershell"></a>Criar uma conta de armazenamento utilizando o PowerShell

O módulo do Azure PowerShell é utilizado para criar e gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts. Este detalhes guia através do PowerShell para criar uma conta de armazenamento do Azure. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este guia de introdução requer a versão do módulo 3.6 ou posterior do Azure PowerShell. Executar `Get-Module -ListAvailable AzureRM` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps)(Instalar o módulo do Azure PowerShell).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão na sua subscrição do Azure com o comando `Login-AzureRmAccount` e siga as instruções no ecrã.

```powershell
Login-AzureRmAccount
```

Se não souber qual localização que pretende utilizar, pode listar as localizações disponíveis. Depois de é apresentada a lista, localize aquela que pretende utilizar. Neste exemplo, vai utilizar **eastus**. Armazene-o numa variável e utilize a variável, pelo que pode alterá-la num único local.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

```powershell
# put resource group in a variable so you can use the same group name going forward
#   without hardcoding it repeatedly
$resourceGroup = "contoso-storage-accounts"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-general-purpose-standard-storage-account"></a>Criar uma conta de armazenamento standard para fins gerais

Existem vários tipos de contas do storage, dependendo de como se passa a ser utilizado e para o serviço (blobs, ficheiros, tabelas ou filas). Esta tabela mostra as possibilidades.

|**Tipo de conta de armazenamento**|**Standard para fins gerais**|**Premium para fins gerais**|**Armazenamento de blobs, camadas de acesso frequente e esporádico**|
|-----|-----|-----|-----|
|**Serviços suportados**| BLOB, ficheiro, tabela, serviços da fila | Serviço Blob | Serviço Blob|
|**Tipos de blobs suportados**|Os blobs de blocos, blobs de páginas, blobs de acréscimo | Blobs de páginas | Blobs de blocos e blobs de acréscimo|

Utilizar [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) para criar uma conta de armazenamento standard para fins gerais que pode utilizar para todos os serviços de quatro. Nome da conta de armazenamento *contosomvcstandard*e configure-a para ter armazenamento localmente redundante e blob encriptação ativada.

```powershell
New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "contosomvcstandard" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não é necessário, pode utilizar o [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) comando para remover o grupo de recursos e todos os recursos relacionados. Neste caso, remove a conta de armazenamento que criou.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Este guia de introdução, criou uma conta de armazenamento standard para fins gerais. Para saber como carregar e transferir blobs com a sua conta de armazenamento, avance para o guia de introdução do armazenamento de Blobs.
> [!div class="nextstepaction"]
> [Transferência de objetos para/de Blob storage do Azure com o PowerShell](../blobs/storage-quickstart-blobs-powershell.md)